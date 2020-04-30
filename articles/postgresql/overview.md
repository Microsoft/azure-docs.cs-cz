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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74481667"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co je Azure Database for PostgreSQL?
Azure Database for PostgreSQL je služba relačních databází v cloudu Microsoftu vytvořená pro vývojáře. Vychází z komunitní verze open source databázového stroje [PostgreSQL](https://www.postgresql.org/) a je k dispozici ve dvou možnostech nasazení: jeden server a škálovatelné (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – Jeden server
Možnost nasazení jediného serveru nabízí:

- Integrovaná [Vysoká dostupnost](concepts-high-availability.md) bez dalších nákladů (99,99% SLA)
- Předvídatelný výkon a všeobecné ceny při průběžných platbách
- [Vertikální škálování podle potřeby](concepts-pricing-tiers.md) během několika sekund
- [Monitorování a upozorňování](concepts-monitoring.md) na posouzení vašeho serveru
- Zabezpečení a dodržování předpisů na podnikové úrovni
- [Zabezpečené pro ochranu](concepts-security.md) citlivých dat v klidovém provozu a v pohybu
- [Automatické zálohování a obnovení k bodu v čase](concepts-business-continuity.md) po dobu až 35 dnů


Všechny tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Díky tomu se můžete soustředit na rychlý vývoj aplikací a urychlení doby uvedení na trh, ale netráví drahých časů a prostředků pro správu virtuálních počítačů a infrastruktury. Můžete pokračovat v vývoji aplikace pomocí Open source nástrojů a platformy podle vašeho výběru, aniž byste se museli učit nové dovednosti.

Možnost nasazení na jeden server nabízí tři cenové úrovně: základní, Pro obecné účely a paměťově optimalizované. Každá úroveň nabízí různé schopnosti prostředků pro podporu vašich úloh databáze. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Podrobnosti najdete v tématu [cenové úrovně](concepts-pricing-tiers.md) .

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL – Citus (škálování)
Možnost Citus () horizontálně škáluje dotazy napříč více počítači pomocí horizontálního dělení. Jeho dotazovací modul parallelizes příchozí dotazy SQL napříč těmito servery, aby bylo rychlejší odezvy na velké datové sady. Obsluhuje aplikace, které vyžadují větší rozsah a výkon, obvykle úlohy, které se blíží--100 GB dat.

Možnost nasazení Citus () nabízí:

- Horizontální škálování napříč několika počítači pomocí horizontálního dělení
- Dotazování paralelního zpracování na těchto serverech pro rychlejší odezvy na velké datové sady
- Vynikající podpora pro víceklientské aplikace, provozní analýzy v reálném čase a transakční úlohy s vysokou propustností

Aplikace vytvořené pro PostgreSQL můžou spouštět distribuované dotazy na úrovni Citus (s standardními [knihovnami připojení](./concepts-connection-libraries.md) a minimálními změnami).

## <a name="contacts"></a>Kontakty
Pokud máte jakékoli dotazy nebo návrhy týkající se práce s Azure Database for PostgreSQL, pošlete e-mail týmu Azure Database for PostgreSQL ([ @Ask Azure DB pro PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Tato adresa je určena pro obecné otázky, nikoli pro lístky podpory.

Dále podle potřeby zvažte tyto kontaktní body:
- Pokud chcete kontaktovat podporu Azure nebo vyřešit problém s vaším účtem, zapište [lístek z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Další kroky
- Porovnání nákladů a kalkulačky najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/) .
- Začněte tím, že vytvoříte první Azure Database for PostgreSQL [jeden server](./quickstart-create-server-database-portal.md) nebo [Citus (škálovatelný)](./quickstart-create-hyperscale-portal.md) .
- Sestavte svou první aplikaci v jazyce Python, PHP, Ruby, C\#, Java, Node.js: [Knihovny připojení](./concepts-connection-libraries.md)
