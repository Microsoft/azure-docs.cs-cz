---
title: Minimální dobou výpadku migrace do Azure Database pro databázi MySQL
description: Tento článek popisuje, jak provést minimální dobou výpadku migraci databáze MySQL do Azure databáze pro databázi MySQL a jak nastavit počáteční zatížení a synchronizaci průběžné dat ze zdrojové databáze na cílovou databázi pomocí společnosti Attunity replikace pro Microsoft Migrace.
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 99add55188615debdc96b6cfc8b21e34552fd9d4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267250"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimální dobou výpadku migrace do Azure Database pro databázi MySQL
Můžete migrovat existující databázi MySQL do databáze Azure pro databázi MySQL pomocí společnosti Attunity replikace pro Migrations Microsoft. Společnosti Attunity replikace je společné nabídky od společnosti Attunity a společnosti Microsoft. Společně se službou migrace databáze Azure je zahrnuta bez dalších poplatků zákazníkům společnosti Microsoft. 

Replikovat společnosti Attunity pomáhá minimalizovat prostoje během migrace databáze, a udržuje provozní v celém procesu zdrojové databáze.

Společnosti Attunity replikace je nástroj replikace dat, která umožňuje synchronizaci dat mezi různé zdroje a cíle. Se rozšíří skriptu pro vytváření schémat a data související s každou databázové tabulky. Replikovat společnosti Attunity nešířily všechny artefakty (například SP, triggery, funkce a tak dále) nebo převést, například, kód PL/SQL, který je hostován v artefaktům T-SQL.

> [!NOTE]
> I když společnosti Attunity replikovat podporuje širokou škálu scénářů migrace, se zaměřuje na podporu pro určitou dílčí sadu párů zdroj a cíl.

Přehled procesu k provedení migrace minimální dobou výpadku zahrnuje:

* **Migrace schématu zdroje MySQL** k databázi Azure pro databázi MySQL spravovaná služba databáze pomocí [MySQL Workbench](https://www.mysql.com/products/workbench/).

* **Nastavení počáteční zatížení a synchronizaci průběžné dat ze zdrojové databáze na cílovou databázi** pomocí společnosti Attunity replikace pro Migrations společnosti Microsoft. Díky tomu minimalizuje čas, který zdrojové databáze musí být nastavena jen pro čtení během přípravy k přepínač aplikace na cílovou databázi MySQL na Azure.

Další informace o společnosti Attunity replikace pro Microsoft Migrations nabídky najdete v následujících zdrojích informací:
 - Přejděte na [společnosti Attunity replikace pro Microsoft Migrations](https://aka.ms/attunity-replicate) webovou stránku.
 - Stáhněte si [společnosti Attunity replikace pro Microsoft migrace](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Přejděte na [společnosti Attunity replikovat komunity](https://aka.ms/attunity-community) úvodní příručku, kurzy a podporu.
 - Podrobný návod k migraci databáze MySQL na Azure Database pro databázi MySQL pomocí společnosti Attunity replikace, najdete v článku [příručka k migraci databáze](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).