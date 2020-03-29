---
title: Výběr distribučních sloupců – Hyperscale (Citus) – Databáze Azure pro PostgreSQL
description: Přečtěte si, jak vybrat distribuční sloupce v běžných scénářích hyperškálování v Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8ced9767d81affceef851820ee587f4f3dd24deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975665"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Výběr distribučních sloupců v Azure Database for PostgreSQL – Hyperscale (Citus)

Výběr distribučního sloupce jednotlivých tabulek je jedním z nejdůležitějších rozhodnutí modelování, které provedete. Azure Database for PostgreSQL – Hyperscale (Citus) ukládá řádky v horizontálních oddílů na základě hodnoty sloupce distribuce řádků.

Správná volba seskupuje související data společně na stejných fyzických uzlech, což rychle zvyšuje počet dotazů a přidává podporu pro všechny funkce SQL. Nesprávná volba umožňuje systém spustit pomalu a nebude podporovat všechny funkce SQL napříč uzly.

Tento článek poskytuje tipy distribuční sloupec pro dva nejběžnější scénáře Hyperscale (Citus).

### <a name="multi-tenant-apps"></a>Aplikace s více tenanty

Architektura s více klienty používá formu modelování hierarchické databáze k distribuci dotazů mezi uzly ve skupině serverů. Horní část hierarchie dat se označuje jako *ID klienta* a musí být uložena ve sloupci v každé tabulce.

Hyperscale (Citus) kontroluje dotazy, aby zjistil, které ID klienta se týkají a najde střep tabulky odpovídající. Směruje dotaz do jednoho pracovního uzlu, který obsahuje šiřidla. Spuštění dotazu se všemi relevantními daty umístěnými ve stejném uzlu se nazývá společné umístění.

Následující diagram znázorňuje společné umístění v datovém modelu s více tenanty. Obsahuje dvě tabulky, účty a kampaně, z nichž každá je distribuována společností `account_id`. Stínované rámečky představují úlomky. Zelené úlomky jsou uloženy společně na jednom uzlu pracovního procesu a modré šmejdy jsou uloženy v jiném uzlu pracovního procesu. Všimněte si, jak má dotaz spojení mezi obchodními vztahy a kampaněmi všechna\_potřebná data na jednom uzlu, když jsou obě tabulky omezeny na stejné ID účtu.

![Kolokace více klientů](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Chcete-li použít tento návrh ve vlastním schématu, zjistěte, co představuje klienta ve vaší aplikaci. Mezi běžné instance patří společnost, účet, organizace nebo zákazník. Název sloupce bude něco `company_id` `customer_id`jako nebo . Zkontrolujte každý z vašich dotazů a zeptejte se sami sebe, by to fungovalo, pokud by měl další klauzule WHERE omezit všechny tabulky účastní řádky se stejným ID klienta?
Dotazy v modelu s více tenanty jsou vymezeny na klienta. Například dotazy na prodej nebo zásoby jsou vymezeny v rámci určitého úložiště.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Rozdělte distribuované\_tabulky podle společného sloupce ID klienta.** Například v aplikaci SaaS, kde jsou\_tenanty společnosti, id\_klienta je pravděpodobně id společnosti.
-   **Převod malých tabulek křížových tenantů na referenční tabulky.** Pokud více klientů sdílí malou tabulku informací, distribuujte ji jako referenční tabulku.
-   **Omezte filtrovat\_všechny dotazy aplikací podle ID klienta.** Každý dotaz by měl požadovat informace pro jednoho klienta najednou.

Přečtěte si [kurz více klientů](./tutorial-design-database-hyperscale-multi-tenant.md) pro příklad, jak vytvořit tento druh aplikace.

### <a name="real-time-apps"></a>Aplikace v reálném čase

Architektura s více tenanty zavádí hierarchickou strukturu a používá společné umístění dat ke směrování dotazů na klienta. Naproti tomu architektury v reálném čase závisí na konkrétnídistribuční vlastnosti jejich dat k dosažení vysoce paralelní zpracování.

Používáme "ID entity" jako termín pro distribuční sloupce v modelu v reálném čase. Typickými entitami jsou uživatelé, hostitelé nebo zařízení.

Dotazy v reálném čase obvykle požadují číselné agregace seskupené podle data nebo kategorie. Hyperscale (Citus) odešle tyto dotazy na každý horizontální oddíl pro částečné výsledky a sestaví konečnou odpověď na uzlu koordinátora. Dotazy běží nejrychleji, když co nejvíce uzlů přispívá a když žádný uzel nesmí provést neúměrné množství práce.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Zvolte sloupec s vysokou mohutností jako distribuční sloupec.** Pro srovnání je pole Stav v tabulce objednávek s hodnotami Nové, Placené a Dodané špatnou volbou sloupce distribuce. Předpokládá pouze těch několik hodnot, které omezuje počet úlomků, které mohou obsahovat data, a počet uzlů, které je mohou zpracovat. Mezi sloupci s vysokou mohutnost, je také vhodné zvolit ty sloupce, které se často používají v klauzule skupiny pro nebo jako klíče spojení.
-   **Zvolte sloupec se sudým rozdělením.** Pokud distribuujete tabulku ve sloupci zkosené na určité běžné hodnoty, data v tabulce mají tendenci se hromadit v určitých šmejdů. Uzly, které drží tyto šimeří nakonec dělat více práce než jiné uzly.
-   **Rozložte tabulky faktů a dimenzí na jejich společné sloupce.**
    Tabulka faktů může mít pouze jeden distribuční klíč. Tabulky, které se připojí k jinému klíči, nebudou společně umístěny s tabulkou faktů. Zvolte jednu dimenzi, kterou chcete společně umístit na základě toho, jak často je spojena a jak často jsou spojovací řádky.
-   **Změňte některé tabulky dimenzí na referenční tabulky.** Pokud tabulku dimenzí nelze společně s tabulkou faktů, můžete zvýšit výkon dotazu distribucí kopií tabulky dimenzí do všech uzlů ve formě referenční tabulky.

Přečtěte si [kurz řídicího panelu](./tutorial-design-database-hyperscale-realtime.md) v reálném čase, kde například můžete vytvořit tento druh aplikace.

### <a name="time-series-data"></a>Data časových řad

V zatížení časových řad aplikace dotaz na nejnovější informace při archivaci starých informací.

Nejčastější chybou při modelování informací o časových řadách v hyperměřítku (Citus) je použití samotného časového razítka jako distribučního sloupce. Distribuce hash založená na čase distribuuje časy zdánlivě náhodně do různých úlomků, nikoli udržování rozsahů času společně v úlomcích. Dotazy, které zahrnují časové rozsahy času, například nejnovější data. Tento typ distribuce hash vede k režii sítě.

#### <a name="best-practices"></a>Osvědčené postupy

-   **Nevybírejte časové razítko jako distribuční sloupec.** Zvolte jiný distribuční sloupec. V aplikaci s více tenanty použijte ID klienta nebo v aplikaci v reálném čase použijte ID entity.
-   **Místo toho použijte dělení tabulky PostgreSQL pro čas.** Pomocí dělení tabulky rozdělit velkou tabulku časem uspořádaných dat do více zděděných tabulek s každou tabulkou obsahující různé časové rozsahy. Distribuce postgres dělené tabulky v Hyperscale (Citus) vytvoří horizontálních oddílů pro zděděné tabulky.

Přečtěte si [kurz časových řad](https://aka.ms/hyperscale-tutorial-timeseries) pro příklad, jak vytvořit tento druh aplikace.

## <a name="next-steps"></a>Další kroky
- Zjistěte, jak [společné umístění](concepts-hyperscale-colocation.md) mezi distribuovanými daty pomáhá dotazům běžet rychle.
