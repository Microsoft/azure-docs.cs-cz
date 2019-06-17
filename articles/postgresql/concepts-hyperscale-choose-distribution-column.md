---
title: Zvolte distribuční sloupce ve službě Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)
description: Vhodná rozhodnutí pro rozdělení sloupce v běžných scénářů hyperškálovatelný systém
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65078982"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Zvolte distribuční sloupce ve službě Azure Database for PostgreSQL – velkokapacitní (Citus) (preview)

Výběr distribučního sloupce každou tabulku je **jeden z vašich nejdůležitějších** modelování rozhodnutí. Velkokapacitní ukládá řádků v horizontálním oddílům podle hodnoty distribučního sloupce řádky.

Správný výběr skupiny související data společně na stejné fyzických uzlů, aby dotazy rychlé a přidává podporu pro všechny součásti SQL. Nesprávná volba díky systému provoz bude pomalý a nebude podporují všechny funkce SQL mezi uzly.

Tato část obsahuje distribuční sloupce tipy pro dva nejběžnější scénáře hyperškálovatelný systém.

### <a name="multi-tenant-apps"></a>Aplikace s více Tenanty

Víceklientská architektura používá formu hierarchické modelování a distribuuje dotazy mezi uzly ve skupině pro server databáze.  Nejvyšší úrovni hierarchie dat se označuje jako *ID tenanta*a musí být uložen ve sloupci pro každou tabulku.

Velkokapacitní kontroluje zjistí, u kterého ID tenanta zahrnují a najde odpovídající horizontálních oddílů tabulky. Dotaz se směruje do jednoho pracovního uzlu, který obsahuje horizontálního oddílu. Spouštění dotazů s všechny relevantní data umístěná na stejném uzlu se nazývá společné umístění.

Následující diagram znázorňuje společné umístění v datovém víceklientského modelu. Obsahuje dvě tabulky, účty a kampaně, rozdělené podle `account_id`. Šedivá představují horizontálních oddílů, z jehož barvu každý představuje, které pracovní uzel obsahuje. Zelená horizontálních oddílů se ukládají společně na jedné pracovní uzel a modrá na jiném. Všimněte si, jak dotaz spojení mezi účty a kampaně by měla všechna potřebná data společně na jednom uzlu při omezování obou tabulek do stejného účtu\_id.

![společné umístění více tenantů](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Chcete-li použít tento návrh ve vlastní schéma, určete, co se považuje za klienta ve vaší aplikaci. Běžné instance zahrnují funkce společnosti, účet, organizace nebo zákazníků. Název sloupce budou vypadat `company_id` nebo `customer_id`. Zkontrolujte každé z vašich dotazů a položte si otázku: by to fungovat, pokud má další klauzule WHERE pro omezení všechny tabulky související s řádky se stejným ID tenanta?
Dotazy v modelu s více tenanty jsou omezené na klienta, například dotazy na prodej nebo z inventáře by zařadit do oboru v rámci určité úložiště.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Oddíl distribuované tabulky podle společné tenanta\_sloupec id.** Například v aplikaci SaaS, kde tenantů se společností, tenant\_id budou pravděpodobně společnosti\_id.
-   **Malé tabulky napříč tenanty převeďte na referenční tabulky.** Když několik tenantů sdílí malé tabulky informací, distribuujte ho jako referenční tabulku.
-   **Omezení filtru všechny aplikace se dotazuje tenantem\_id.** Každý dotaz by měl požádat o informace pro jednoho tenanta v čase.

Čtení [kurzu práce s více tenanty](./tutorial-design-database-hyperscale-multi-tenant.md) Příklad sestavování tohoto typu aplikace.

### <a name="real-time-apps"></a>Aplikací v reálném čase

Víceklientská architektura představuje hierarchickou strukturu a používá společné umístění dat ke směrování dotazů na tenanta. Naopak architektury v reálném čase, závisí na konkrétní distribuční vlastnosti svoje data k zajištění vysoce paralelní zpracování.

"Entity ID" používáme jako označení pro rozdělení sloupců v modelu v reálném čase. Typické entity jsou uživatelé, hostitelů nebo zařízení.

Dotazy v reálném čase obvykle požádat o číselné agregace seskupené podle data nebo kategorie. Velkokapacitní odesílá dotazy pro každý horizontální oddíl pro částečné výsledky a sestaví konečné odpovědí na koordinační uzel. Dotazy spustit nejrychlejší kdy co nejvíce přispívá mnoha uzly a když žádné jeden uzel musí neúměrné množství práce.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Zvolte sloupec s vysokou kardinalitou jako sloupec distribuce.** Pro porovnání \"stav\" pole v tabulce objednávky s hodnotami "nové", "placené" a "dodán" je špatně zvolený distribuční sloupce. Předpokládá pouze několik hodnoty, což omezuje počet horizontálních dělení, které mohou obsahovat data a počet uzlů, které může zpracovat. Mezi sloupce s vysokou kardinalitou je dobré navíc zvolit ty, které jsou často používány v klauzulí group by nebo join klíče.
-   **Vyberte sloupec s rovnoměrná distribuce.** Pokud distribuujete tabulku podle sloupce zkosený určité společné hodnoty, data v tabulce se mají shromáždit v některých horizontálních oddílů. Uzly uchovávající těchto horizontálních oddílů přestane dělat více práce než jiné uzly.
-   **Distribuujte tabulkami faktů a dimenzí na jejich společné sloupce.**
    Vaše tabulka faktů může mít pouze jeden distribuční klíč. Tabulky, které na jiný klíč nesmí být umístěna společně s tabulkou faktů. Zvolte jeden dimenze společné umístění na základě četnosti je připojen a velikosti spojování řádků.
-   **Změňte některé tabulky dimenzí v referenční tabulky.** Pokud tabulky dimenze nelze společně umísťovat s tabulkou faktů, může zlepšit výkon dotazů díky distribuci kopií tabulky dimenze do všech uzlů v podobě referenční tabulku.

Čtení [řídicího panelu v reálném čase kurzu](./tutorial-design-database-hyperscale-realtime.md) Příklad sestavování tohoto typu aplikace.

### <a name="timeseries-data"></a>Data časové řady

V úloze časových řad aplikace dotazují informacemi z nedávné doby při archivaci zastaralé informace.

Nejběžnější chybu v časové řadě informace v Hyperškálovacím modelování používá časové razítko sama jako distribučního sloupce. Distribuci algoritmus hash na základě času bude distribuovat časy zdánlivě náhodně do různých horizontálních oddílech spíše než udržování rozsahů doby společně v horizontálních oddílech. Dotazy zahrnující čas obecně odkazovat rozsahy času (například nejnovější data), takže taková distribuci algoritmus hash by mohlo dojít k sítě režie.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Nevybírejte časové razítko jako sloupec distribuce.** Vyberte jiný distribuční sloupce. V aplikaci s více tenanty použijte ID tenanta, nebo v aplikaci v reálném čase použijte ID entity.
-   **Použijte PostgreSQL dělení tabulky dobu místo.** Dělení tabulky použijte k rozdělení velké tabulky časově řazenou dat do několika tabulek zděděné s každým obsahující různé časové rozsahy.  Distribuce Postgres dělené tabulky v Hyperškálovacím vytvoří horizontálních oddílů pro zděděné tabulky.

Čtení [časové řady kurzu](https://aka.ms/hyperscale-tutorial-timeseries) Příklad sestavování tohoto typu aplikace.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [společné umístění](concepts-hyperscale-colocation.md) mezi distribuovaných dat pomáhá rychle spouštět dotazy
