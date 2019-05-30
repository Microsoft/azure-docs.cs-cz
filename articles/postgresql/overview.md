---
title: Přehled služby relačních databází Azure Database for PostgreSQL
description: Přehled služby relačních databází Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: f4023fa84215a0319669de0d812d8306b62278e3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073272"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co je Azure Database for PostgreSQL?
Azure Database for PostgreSQL je služba relačních databází v cloudu Microsoftu vytvořená pro vývojáře. Je založen na komunitní verze open source [PostgreSQL](https://www.postgresql.org/) databázový stroj a je k dispozici dvě možnosti nasazení: Jeden Server a velkokapacitní (Citus) (preview).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – jeden Server
Nabízí možnost nasazení jednoho serveru:

- Integrovanou vysokou dostupnost (99,99 % smlouva SLA) bez dalších poplatků
- Předvídatelný výkon a všeobecné ceny při průběžných platbách
- Vertikální škálování podle potřeby během několika sekund
- Monitorování a upozorňování rychle posoudit dopad vertikálního
- Zabezpečení pro ochranu neaktivních uložených dat i přenášených dat
- Automatické zálohování a obnovení k určitému bodu v čase po dobu až 35 dnů
- Zabezpečení a dodržování předpisů na podnikové úrovni

Všechny tyto možnosti nevyžadují téměř žádnou správu a jsou k dispozici bez dalších poplatků. Umožňují vám soustředit se na rychlý vývoj aplikací a zkracuje dobu uvedení na trh, namísto trávit cenný čas a prostředky ke správě virtuálních počítačů a infrastruktury. Můžete pokračovat k vývoji aplikací pomocí opensourcových nástrojů a platformy podle vašeho výběru, aniž byste museli učit nové dovednosti.

Možnost nasazení jednoho serveru nabízí tři cenové úrovně: Basic, pro obecné účely a paměťově optimalizovaná. Každá úroveň nabízí různé schopnosti prostředků pro podporu vašich úloh databáze. Svou první aplikaci můžete postavit na malé databázi za pár dolarů na měsíc a pak upravit škálování tak, aby vyhovovalo potřebám vašeho řešení. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, a jen tehdy, když je potřebujete. Zobrazit [cenové úrovně](concepts-pricing-tiers.md) podrobnosti.

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)
Možnost Hyperškálovatelného (Citus) vodorovně škáluje dotazy napříč více počítačů pomocí horizontálního dělení. Jeho dotazovací modul parallelizes příchozích dotazů SQL na těchto serverů pro rychlejší odpovědi na rozsáhlých datových sadách. Aplikace, které vyžadují větší škálovatelnost a výkon, obvykle úlohy, které se blíží--nebo již překračují – 100 GB dat slouží.

Nabízí možnost nasazení Hyperškálovatelného (Citus):

- Vodorovné škálování na více počítačů pomocí horizontálního dělení
- Využitím paralelizace dotazů na těchto serverů pro rychlejší odpovědi na rozsáhlých datových sadách
- Skvělou podporu pro aplikace s více tenanty, operační analýza v reálném čase a Vysoká propustnost transakčními úlohami

Aplikace vytvořené pro PostgreSQL můžete spouštění distribuovaných dotazů na Hyperškálovatelného (Citus) se standardem [knihovny připojení](./concepts-connection-libraries.md) a minimální změny.

Všimněte si, že Hyperškálovatelného (Citus) je ve verzi public preview a jako takový zatím nenabízí smlouvu SLA.

## <a name="data-security"></a>Zabezpečení dat
Azure Database for PostgreSQL zachovává tradici zabezpečení dat v Azure databázové služby. Obsahuje funkce, které omezují přístup, chránit data v klidovém stavu a za provozu a vám pomohou monitorovat aktivitu. Informace o zabezpečení platformy Azure najdete v [Centru zabezpečení Azure](https://azure.microsoft.com/overview/trusted-cloud/).

Azure Database for postgresql – služba používá šifrování úložiště pro data v klidovém stavu a je kompatibilní s FIPS 140-2. Data včetně záloh se šifrují na disku. Služba používá šifrování AES 256 bitů součástí šifrování úložiště Azure a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat. Ve výchozím nastavení služba Azure Database for PostgreSQL vyžaduje zabezpečené připojení pro data přenášená přes síť a mezi databází a kódem klientské aplikace.

## <a name="contacts"></a>Kontakty
Pro jakékoli dotazy nebo návrhy o práci s využitím Azure Database for PostgreSQL, pošlete e-mail na databázi Azure pro PostgreSQL týmu ([ @Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Tato adresa se pro obecné dotazy spíše než lístky podpory.

Kromě toho, zvažte následující body kontaktu podle potřeby:
- Obraťte se na podporu Azure nebo vyřešit problém s vaším účtem [lístek na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Další postup
- Na [stránce s cenami](https://azure.microsoft.com/pricing/details/postgresql/) najdete porovnání nákladů a kalkulačky.
- Začněte vytvořením první databáze Azure Database for PostgreSQL [jeden Server](./quickstart-create-server-database-portal.md) nebo [Hyperškálovatelného (Citus) (preview)](./quickstart-create-hyperscale-portal.md)
- Vytvořte svoji první aplikaci v prostředí typu Python, PHP, Ruby, C\#, Java, Node.js: [Knihovny připojení](./concepts-connection-libraries.md)
