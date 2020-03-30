---
title: Přehled služby relačních databází Azure Database for PostgreSQL
description: Přehled služby relačních databází Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 9ea0610811f6906526afe55d577e04a8decd5f49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74481667"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co je Azure Database for PostgreSQL?
Azure Database for PostgreSQL je relační databázová služba v cloudu Microsoftu vytvořená pro vývojáře. Je založen na komunitní verzi open-source databázového stroje [PostgreSQL](https://www.postgresql.org/) a je k dispozici ve dvou možnostech nasazení: Single Server a Hyperscale (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – Jeden server
Možnost nasazení jednoho serveru poskytuje:

- Vestavěná [vysoká dostupnost](concepts-high-availability.md) bez dalších nákladů (99.99% SLA)
- Předvídatelný výkon a všeobecné ceny při průběžných platbách
- [Vertikální měřítko podle potřeby](concepts-pricing-tiers.md) během několika sekund
- [Sledování a upozorňování](concepts-monitoring.md) na posouzení serveru
- Zabezpečení a dodržování předpisů na podnikové úrovni
- [Zabezpečeno pro ochranu](concepts-security.md) citlivých dat v klidovém stavu a v pohybu
- [Automatické zálohování a obnovení bodu v čase](concepts-business-continuity.md) po dobu až 35 dnů


Všechny tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Umožňují vám zaměřit se na rychlý vývoj aplikací a urychlení vašeho času na trhu, spíše než trávit drahocenný čas a zdroje pro správu virtuálních počítačů a infrastruktury. Můžete pokračovat ve vývoji aplikace pomocí open-source nástrojů a platformy dle vašeho výběru, aniž byste se museli učit nové dovednosti.

Možnost nasazení jednoho serveru nabízí tři cenové úrovně: Základní, Obecné účely a Optimalizované pro paměť. Každá úroveň nabízí různé schopnosti prostředků pro podporu vašich úloh databáze. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Podrobnosti [najdete v tématu Cenové úrovně.](concepts-pricing-tiers.md)

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Databáze Azure pro PostgreSQL – hyperškálování (Citus)
Možnost Hyperscale (Citus) vodorovně škáluje dotazy napříč více počítači pomocí horizontálního dělení. Jeho dotazovací stroj paralelizuje příchozí dotazy SQL napříč těmito servery pro rychlejší odpovědi na velké datové sady. Slouží aplikacím, které vyžadují větší škálování a výkon, obecně úlohy, které se blíží – nebo již překračují – 100 GB dat.

Možnost nasazení Hyperscale (Citus) poskytuje:

- Horizontální škálování na více počítačích pomocí horizontálního dělení
- Pro rychlejší odpovědi na velké datové sady se dotazovat na paralelizaci těchto serverů
- Vynikající podpora pro víceklientské aplikace, provozní analýzy v reálném čase a transakční úlohy s vysokou propustností

Aplikace vytvořené pro PostgreSQL mohou spouštět distribuované dotazy na Hyperscale (Citus) se [standardními knihovnami připojení](./concepts-connection-libraries.md) a minimálními změnami.

## <a name="contacts"></a>Kontakty
Máte-li jakékoli dotazy nebo návrhy týkající se práce s Databází Azure pro PostgreSQL, pošlete e-mail do databáze Azure pro PostgreSQL Team[ @Ask (Azure DB for PostgreSQL).](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) Tato adresa je určena spíše pro obecné otázky než pro podporu vstupenek.

Kromě toho zvažte tato kontaktní místa podle potřeby:
- Pokud chcete kontaktovat podporu Azure nebo vyřešit problém se svým [účtem, nasuňte lístek z webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Další kroky
- Porovnání nákladů a kalkulačky [najdete](https://azure.microsoft.com/pricing/details/postgresql/) na stránce s cenami.
- Začínáme vytvořením první databáze Azure pro postgreSQL [single server](./quickstart-create-server-database-portal.md) nebo [hyperškálování (Citus)](./quickstart-create-hyperscale-portal.md)
- Sestavte svou první aplikaci v jazyce Python, PHP, Ruby, C\#, Java, Node.js: [Knihovny připojení](./concepts-connection-libraries.md)
