---
title: Výběr distribučních sloupců – Citus (Velká měřítko) – Azure Database for PostgreSQL
description: Naučte se, jak vybrat distribuční sloupce v běžných scénářích Azure Database for PostgreSQL – Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 129eff8c954c0c5469d3607e6ae16ce3202630ed
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929332"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Výběr distribučních sloupců v Azure Database for PostgreSQL – Citus (škálování)

Výběr distribučního sloupce tabulky představuje jedno z nejdůležitějších rozhodnutí při modelování. Azure Database for PostgreSQL – Citus () ukládá řádky v horizontálních oddílů na základě hodnoty distribučního sloupce řádky.

Správné skupiny voleb vztahující se k datům společně na stejných fyzických uzlech, které urychlují dotazy a přidávají podporu pro všechny funkce SQL. Nesprávná volba způsobí, že systém pracuje pomalu a nepodporuje všechny funkce SQL napříč uzly.

Tento článek obsahuje popisy pro distribuční sloupce dvou nejběžnějších scénářů Citus (s větším měřítkem).

### <a name="multi-tenant-apps"></a>Aplikace s více tenanty

Architektura s více klienty používá formu hierarchického modelování databáze pro distribuci dotazů mezi uzly ve skupině serverů. Horní část hierarchie dat se označuje jako *ID tenanta* a musí se ukládat do sloupce v každé tabulce.

Citus () kontroluje dotazy, aby se zobrazilo, které ID tenanta zahrnuje a vyhledá vyhovující tabulku horizontálních oddílů. Směruje dotaz na jeden pracovní uzel, který obsahuje horizontálních oddílů. Spuštění dotazu se všemi relevantními daty umístěnými na stejném uzlu se nazývá společné umístění.

Následující diagram znázorňuje společné umístění v datovém modelu s více klienty. Obsahuje dvě tabulky, účty a kampaně, z nichž každý distribuuje `account_id` . Šedivá pole reprezentují horizontálních oddílů. Zelený horizontálních oddílů je uložen společně na jednom pracovním uzlu a modrý horizontálních oddílů je uložený na jiném pracovním uzlu. Všimněte si, jak dotaz spojení mezi účty a kampaněmi obsahuje všechna data potřebná na jednom uzlu, pokud jsou obě tabulky omezené na stejné \_ ID účtu.

![Souběžné umístění pro více tenantů](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Chcete-li tento návrh použít ve vašem vlastním schématu, určete, co v aplikaci znamená klienta. Mezi běžné instance patří společnost, účet, organizace nebo zákazník. Název sloupce bude něco podobného `company_id` nebo `customer_id` . Prověřte jednotlivé dotazy a položte si je, kdyby fungovaly, pokud měly další klauzule WHERE k omezení všech tabulek zahrnutých do řádků se stejným ID tenanta?
Dotazy v modelu víceklientské architektury jsou vymezeny na tenanta. Například dotazy na prodej nebo inventář jsou vymezeny v rámci určitého úložiště.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Rozdělení distribuovaných tabulek pomocí společného \_ sloupce ID tenanta** Například v aplikaci SaaS, kde jsou vzdálení klienti, se ID tenanta \_ pravděpodobně považuje za \_ ID společnosti.
-   **Převod malých tabulek pro více tenantů na referenční tabulky.** Pokud více klientů sdílí malou tabulku informací, distribuujte ji jako referenční tabulku.
-   **Omezí filtrování všech dotazů aplikace podle \_ ID tenanta.** Každý dotaz by měl požadovat informace pro jednoho klienta v jednom okamžiku.

Příklad, jak tento druh aplikace sestavit, najdete v [kurzu pro více tenantů](./tutorial-design-database-hyperscale-multi-tenant.md) .

### <a name="real-time-apps"></a>Aplikace v reálném čase

Architektura víceklientské architektury zavádí hierarchickou strukturu a používá společné umístění dat ke směrování dotazů na tenanta. Naproti tomu architektury v reálném čase závisí na konkrétních vlastnostech distribuce jejich dat k dosažení vysoce paralelního zpracování.

Jako termín pro distribuční sloupce v modelu reálného času používáme "ID entity". Typickými entitami jsou uživatelé, hostitelé nebo zařízení.

Dotazy v reálném čase obvykle požadují číselné agregace seskupené podle data nebo kategorie. Citus () odesílá dotazy do každého horizontálních oddílů pro částečné výsledky a sestaví konečnou odpověď na uzel koordinátora. Dotazy budou spouštěny nejrychleji, když co je možné co nejvíce přispívat, a pokud žádný jeden uzel nemůže provést neúměrný objem práce.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Vyberte sloupec s vysokou mohutnosti jako distribuční sloupec.** Pro porovnání je pole stav v tabulce Order s hodnotami New, placed a expedováno špatným výběrem distribučního sloupce. Předpokládá jenom několik hodnot, které omezují počet horizontálních oddílů, která můžou uchovávat data, a počet uzlů, které ho můžou zpracovat. Mezi sloupci s vysokou mohutnosti je také vhodné zvolit tyto sloupce, které se často používají v klauzulích Group by nebo as JOIN.
-   **Vyberte sloupec s ještě distribucí.** Pokud rozšíříte tabulku na sloupec zkosených na určité běžné hodnoty, data v tabulce budou v určitých horizontálních oddílůch sečtena. Uzly, které obsahují tyto horizontálních oddílů, končí více práce než jiné uzly.
-   **Rozdělte tabulky faktů a dimenzí na své společné sloupce.**
    Tabulka faktů může mít pouze jeden distribuční klíč. Tabulky, které se připojují k jinému klíči, nebudou společně umístěny v tabulce faktů. Vyberte jednu dimenzi, která se bude hledat na základě toho, jak často je připojená, a velikosti spojovacích řádků.
-   **Změňte některé tabulky dimenzí na referenční tabulky.** Pokud tabulka dimenze nemůže být společně umístěná v tabulce faktů, můžete zlepšit výkon dotazu distribucí kopií tabulky dimenzí do všech uzlů ve formě referenční tabulky.

Příklad vytvoření tohoto typu aplikace najdete v [kurzu řídicího panelu v reálném čase](./tutorial-design-database-hyperscale-realtime.md) .

### <a name="time-series-data"></a>Data časové řady

V rámci úlohy časových řad aplikace při archivaci starých informací dotazují poslední informace.

Nejběžnější Chyba při modelování informací o datové řadě v Citus () je použití samotného časového razítka jako distribučního sloupce. Rozdělení hodnoty hash na základě času distribuuje náhodné časy do různých horizontálních oddílů A neudržuje rozsahy času společně v horizontálních oddílů. Dotazy, které zahrnují čas obvykle odkazují na časové rozsahy, například nejnovější data. Tento typ distribuce hodnoty hash vede k zatížení sítě.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Nevybírejte jako distribuční sloupec časové razítko.** Vyberte jiný distribuční sloupec. V aplikaci s více klienty použijte ID tenanta nebo v aplikaci v reálném čase použijte ID entity.
-   **Místo toho použijte dělení tabulky PostgreSQL.** Pomocí dělení tabulky můžete rozdělit velkou tabulku časově uspořádaných dat do několika zděděných tabulek s každou tabulkou, která obsahuje různé časové rozsahy. Distribuce tabulky Postgres-partition v Citus () vytvoří horizontálních oddílů pro zděděné tabulky.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak společné [umístění](concepts-hyperscale-colocation.md) mezi distribuovanými daty pomáhá rychle spustit dotazy.
