---
title: Horizontální navýšení kapacity
description: Vývojáři SaaS (software jako služba) můžou snadno vytvářet elastické a škálovatelné databáze v cloudu pomocí těchto nástrojů.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 44c6147402cbe05e62c091863cb0bd4f9235bfab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061650"
---
# <a name="scaling-out-with-azure-sql-database"></a>Horizontální navýšení kapacity s Azure SQL Database
Pomocí nástrojů **elastic Database** můžete snadno ŠKÁLOVAT databáze SQL Azure. Pomocí těchto nástrojů a funkcí můžete vytvářet řešení pro transakční úlohy a zejména aplikace SaaS (software jako služba), které jsou k **disAzure SQL Database** . Funkce Elastic Database se skládají z:

* [Elastic Database Klientská knihovna](sql-database-elastic-database-client-library.md): Klientská knihovna je funkce, která umožňuje vytvářet a udržovat databáze horizontálně dělené.  Přečtěte si téma Začínáme [s nástroji pro elastic Database](sql-database-elastic-scale-get-started.md).
* [Elastic Database Nástroj pro dělení a slučování](sql-database-elastic-scale-overview-split-and-merge.md): přesouvá data mezi databázemi horizontálně dělené. Tento nástroj je užitečný pro přesun dat z víceklientské databáze do databáze s jedním tenanta (nebo naopak). Viz [kurz k nástroji pro dělení a slučování elastické databáze](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Úlohy elastické databáze](elastic-jobs-overview.md): použijte úlohy pro správu velkého počtu databází Azure SQL. Pomocí úloh můžete snadno provádět operace správy, jako jsou změny schématu, Správa přihlašovacích údajů, aktualizace referenčních dat, shromažďování dat výkonu nebo shromažďování telemetrie klienta (zákazníka).
* [Elastic Database dotaz](sql-database-elastic-query-overview.md) (Preview): umožňuje spustit dotaz Transact-SQL, který zahrnuje více databází. To umožňuje připojení k nástrojům pro vytváření sestav, jako je Excel, Power BI, Tableau atd.
* [Elastické transakce](sql-database-elastic-transactions-overview.md): Tato funkce umožňuje spouštět transakce, které přesahují několik databází v Azure SQL Database. Transakce elastické databáze jsou k dispozici pro aplikace .NET s využitím rozhraní ADO .NET a jsou integrovány se známým programovacím prostředím pomocí [tříd System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

Následující obrázek ukazuje architekturu, která obsahuje **funkce elastic Database** ve vztahu ke kolekci databází.

V tomto obrázku barvy databáze reprezentují schémata. Databáze se stejnou barevou sdílejí stejné schéma.

1. Sada **databází SQL Azure** je hostovaná v Azure pomocí architektury horizontálního dělení.
2. **Klientská knihovna elastic Database** se používá ke správě horizontálních oddílů sady.
3. Podmnožina databází je vložena do **elastického fondu**. (Podívejte [se, co je to fond?](sql-database-elastic-pool.md)).
4. **Úloha elastic Database** spouští naplánované nebo ad hoc skripty T-SQL pro všechny databáze.
5. **Nástroj pro dělení na sloučení** se používá k přesunu dat z jednoho horizontálních oddílů do jiného.
6. **Dotaz elastic Database** umožňuje napsat dotaz, který zahrnuje všechny databáze ve horizontálních oddílů sadě.
7. **Elastické transakce** umožňují spouštět transakce, které jsou rozloženy na více databází. 

![Nástroje pro elastické databáze][1]

## <a name="why-use-the-tools"></a>Proč používat nástroje?
Zajištění pružnosti a škálování pro cloudové aplikace je pro virtuální počítače a úložiště objektů BLOB jednoduché – stačí přidat nebo odečíst jednotky nebo zvýšit výkon. Ale u relačních databází zůstala výzva pro zpracování stavových dat. V těchto scénářích se objevují problémy:

* Rozšiřování a zmenšení kapacity relační databáze v rámci vaší úlohy.
* Správa aktivních oblastí, které mohou vzniknout vlivem na určitou podmnožinu dat, jako je například zaneprázdněný koncový zákazník (tenant).

V tradičních scénářích, jako jsou ty, se tyto scénáře vyřešily investováním do větších databázových serverů, které podporují aplikaci. Tato možnost je však omezená v cloudu, kde se veškeré zpracování provádí na předdefinovaném komoditním hardwaru. Místo toho můžete distribuovat data a zpracování v mnoha identicky strukturovaných databázích (model škálování na více instancí označovaný jako "horizontálního dělení"), a to z hlediska nákladů i pružnosti.

## <a name="horizontal-and-vertical-scaling"></a>Horizontální a svislé škálování
Následující obrázek ukazuje vodorovné a svislé rozměry měřítka, což jsou základní způsoby škálování elastických databází.

![Horizontální a vertikální horizontální navýšení kapacity][2]

Horizontální škálování odkazuje na přidání nebo odebrání databází, aby bylo možné upravit kapacitu nebo celkový výkon, označovaný také jako "škálování na více instancí". Horizontálního dělení, ve kterém jsou data rozdělená mezi kolekce identicky strukturovaných databází, představuje běžný způsob implementace horizontálního škálování.  

Vertikální škálování odkazuje na zvýšení nebo snížení velikosti výpočetní aplikace v jednotlivých databázích, označované také jako "škálování nahoru".

Většina databázových aplikací v cloudovém měřítku používá kombinaci těchto dvou strategií. Například software jako aplikace služby může používat horizontální škálování ke zřízení nových koncových zákazníků a vertikálního škálování, aby každá databáze koncových zákazníků mohla zvětšovat nebo zmenšovat prostředky podle potřeby zatížení.

* Horizontální škálování je spravováno pomocí [klientské knihovny elastic Database](sql-database-elastic-database-client-library.md).
* Vertikální škálování se dosahuje pomocí rutin Azure PowerShell ke změně úrovně služby nebo umístěním databází do elastického fondu.

## <a name="sharding"></a>Sharding
*Horizontální dělení* je technika distribuce velkých objemů dat, která mají stejnou strukturu, mezi více nezávislých databází. Je obzvláště populární pro vývojáře v cloudu, který vytváří nabídky SAAS (software jako služba) pro koncové zákazníky nebo firmy. Tito koncoví zákazníci se často označují jako "klienti". Horizontálního dělení může být vyžadováno z libovolného počtu důvodů:  

* Celkový objem dat je moc velký, aby se vešel do omezení individuální databáze.
* Propustnost transakce celkového zatížení překračuje možnosti individuální databáze.
* Klienti můžou od sebe od sebe vyžadovat fyzickou izolaci, takže pro každého tenanta jsou potřeba samostatné databáze.
* Různé oddíly databáze mohou být potřeba v různých geografických oblastech pro účely dodržování předpisů, výkonu nebo geopolitických důvodů.

V jiných scénářích, jako jsou například ingestování dat z distribuovaných zařízení, lze horizontálního dělení použít k vyplnění sady databází, které jsou uspořádány do dočasného formátu. Například samostatná databáze může být vyhrazená pro každý den nebo týden. V takovém případě může být horizontálního dělení klíč celé číslo představující datum (přítomné ve všech řádcích tabulek horizontálně dělené) a dotazy, které načítají informace pro rozsah kalendářních dat, musí být směrovány pomocí aplikace do podmnožiny databází pokrývajících daný rozsah.

Horizontálního dělení funguje nejlépe, když je možné každou transakci v aplikaci omezit na jednu hodnotu horizontálního dělení klíče. Tím zajistíte, aby všechny transakce byly místní pro určitou databázi.

## <a name="multi-tenant-and-single-tenant"></a>Vícenásobný tenant a jeden tenant
Některé aplikace používají nejsnadnější přístup k vytvoření samostatné databáze pro každého tenanta. Tento přístup je **jedním z horizontálního dělení vzorů tenanta** , který poskytuje izolaci, možnosti zálohování a obnovení a škálování prostředků v členitosti klienta. U jednoho tenanta horizontálního dělení je každá databáze přidružená ke konkrétní hodnotě ID tenanta (nebo hodnotě klíče zákazníka), ale tento klíč nemusí být vždy přítomen v samotných datech. Je zodpovědností aplikace při směrování jednotlivých požadavků do příslušné databáze a Klientská knihovna to může zjednodušit.

![Jeden tenant versus více tenantů][4]

Jiné scénáře začlení více tenantů do databází, nikoli jejich izolování do samostatných databází. Tento model je typický **horizontálního dělení vzor pro více tenantů** a může být řízený skutečností, že aplikace spravuje velký počet malých klientů. V horizontálního dělení s více klienty jsou řádky v databázových tabulkách všechny navržené tak, aby identifikovaly klíč identifikující ID tenanta nebo klíč horizontálního dělení. Aplikační vrstva je znovu zodpovědná za směrování požadavku tenanta do příslušné databáze a to může být podporováno klientskou knihovnou elastické databáze. Kromě toho je možné pomocí zabezpečení na úrovni řádků filtrovat, ke kterým řádkům má každý tenant přístup – podrobnosti najdete v tématu [víceklientské aplikace s nástroji elastické databáze a zabezpečení na úrovni řádků](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuce dat mezi databázemi může být potřeba u více tenantů horizontálního dělení vzoru a usnadňuje ho Nástroj pro dělení a slučování elastické databáze. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Přesun dat z několika do databází s jednou tenantů
Při vytváření aplikace v SaaS je typický zákazníkům nabízet zkušební verzi softwaru pro potenciální zákazníky. V tomto případě je cenově výhodnější používat pro data víceklientské databáze. Pokud se ale potenciální zákazník stal zákazníkem, je databáze jednoho tenanta lepší, protože poskytuje lepší výkon. Pokud zákazník vytvořil během zkušebního období data, přesuňte data z více tenantů do nové databáze s jedním klientem pomocí [Nástroje pro dělení](sql-database-elastic-scale-overview-split-and-merge.md) na více tenantů.

## <a name="next-steps"></a>Další kroky
Ukázkovou aplikaci, která demonstruje knihovnu klienta, najdete v tématu Začínáme [s nástroji pro elastic Database](sql-database-elastic-scale-get-started.md).

Pokud chcete převést existující databáze na používání nástrojů, přečtěte si téma [migrace existujících databází pro horizontální](sql-database-elastic-convert-to-use-elastic-tools.md)navýšení kapacity.

Pokud chcete zobrazit konkrétní informace o elastickém fondu, Projděte si téma [cenové a výkonové požadavky pro elastický fond](sql-database-elastic-pool.md)nebo vytvořte nový fond s [elastickými fondy](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

