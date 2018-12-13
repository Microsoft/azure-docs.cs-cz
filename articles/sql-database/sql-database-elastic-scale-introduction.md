---
title: Horizontální navýšení kapacity s Azure SQL Database | Dokumentace Microsoftu
description: Software jako služba (SaaS) vývojáři můžete snadno vytvářet škálovatelné, elastické databáze v cloudu za použití těchto nástrojů
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: a36c5cfc977920f43b7f73e3e7cf9176de7c1f8a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867082"
---
# <a name="scaling-out-with-azure-sql-database"></a>Horizontální navýšení kapacity s Azure SQL Database
Můžete snadno škálovat databází Azure SQL Database pomocí **Elastic Database** nástroje. Tyto nástroje a funkce umožňují pracovat s prostředky databáze **Azure SQL Database** k vytváření řešení pro transakční úlohy a zvlášť Software jako služba (SaaS) aplikací. Funkce elastic Database se skládají z:

* [Klientská knihovna elastic Database](sql-database-elastic-database-client-library.md): klientské knihovny je funkce, která vám umožní vytvořit a udržovat horizontálně dělené databáze.  Zobrazit [Začínáme s nástroji Elastic Database](sql-database-elastic-scale-get-started.md).
* [Elastické databáze dělení a slučování](sql-database-elastic-scale-overview-split-and-merge.md): přesouvá data mezi horizontálně dělené databáze. Tento nástroj je užitečný pro přesun dat z databáze více tenantů do jednoho tenanta databáze (nebo naopak). Zobrazit [kurz nástroj Elastic database dělení a slučování](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Úlohy elastic Database](sql-database-elastic-jobs-overview.md) (preview): použití úlohy pro správu velkého počtu databází Azure SQL. Snadno proveďte operace správy, například změny schématu, Správa přihlašovacích údajů, aktualizace referenčních dat, shromažďování údajů o výkonu nebo shromažďování telemetrických dat tenanta (zákazníka) pomocí úlohy.
* [Dotaz na elastic Database](sql-database-elastic-query-overview.md) (preview): umožňuje spustit dotaz jazyka Transact-SQL, která zahrnuje více databází. To umožňuje připojení k vytváření sestav nástrojů, jako je Excel, Power BI, Tableau, atd.
* [Elastické transakce](sql-database-elastic-transactions-overview.md): Tato funkce umožňuje spuštění transakce, které zahrnují několik databází ve službě Azure SQL Database. Transakcí elastické databáze jsou k dispozici pro aplikace .NET pomocí rozhraní ADO .NET a integrovat známé programování pomocí prostředí [System.Transaction třídy](https://msdn.microsoft.com/library/system.transactions.aspx).

Následující obrázek znázorňuje architekturu, která zahrnuje **funkce Elastic Database** ve vztahu k kolekce databází.

Barvy databáze na tomto obrázku představují schémata. Databáze se stejnou barvu sdílejí stejné schéma.

1. Sada **databází Azure SQL** je hostovaná v Azure s využitím architektury horizontálního dělení.
2. **Klientská knihovna Elastic Database** slouží ke správě nastavení horizontálními oddíly.
3. Podmnožinu databází přejde do **elastického fondu**. (Viz [co je to fond?](sql-database-elastic-pool.md)).
4. **Úlohy elastické databáze** spouští ručně, nebo ad hoc skriptů T-SQL pro všemi databázemi.
5. **Nástroj split-merge** se používá k přesunu dat z jednoho horizontálního oddílu do jiného.
6. **Dotaz na elastickou databázi** umožňuje napsat dotaz, který zahrnuje všechny databáze v nastavení horizontálními oddíly.
7. **Elastické transakce** umožňují provozovat transakcí, které zahrnují několik databází. 

![Nástroje pro elastické databáze][1]

## <a name="why-use-the-tools"></a>Proč používat nástroje?
Dosažení pružnost a škálování cloudových aplikací byl jednoduchý pro virtuální počítače a úložiště objektů blob – jednoduše přidávat nebo odebírat jednotky, případně zvyšte power. Ale zůstal výzvu pro stavové zpracování dat v relačních databázích. Problémy se umístila v těchto scénářích:

* Zvětšování a zmenšování kapacitu pro relační databáze součástí vašich úloh.
* Správa hotspotů, které mohou vzniknout by to mělo dopad na konkrétní podmnožinu dat – například zaneprázdněný koncových zákazníků (klientů).

Tradičně scénáře, jako jsou tyto vyřeší Investujete do pracovat ve větším měřítku databázové servery k podpoře aplikace. Tato možnost je však omezená v cloudu, kde se veškeré zpracování stane na předdefinované komoditním hardwaru. Místo toho distribuci dat a zpracování v rozsáhlé skupině databází stejně jako strukturované (označované jako "horizontálního dělení" vzor horizontální navýšení kapacity) poskytuje alternativu k tradiční přístupy škálovat z hlediska nákladů a pružnost.

## <a name="horizontal-and-vertical-scaling"></a>Vodorovné a svislé škálování
Následující obrázek znázorňuje vodorovného a svislého rozměry škálování, které jsou základní způsoby, které je možné škálovat elastických databází.

![Vodorovné a svislé horizontální navýšení kapacity][2]

Horizontální škálování odkazuje na přidáním nebo odebráním databází, abyste mohli upravit kapacitu nebo celkový výkon, také vyznačeny "škálování". Horizontální dělení, ve kterém data jsou rozdělená mezi kolekce stejně jako strukturované databází je běžný způsob, jak implementovat horizontální škálování.  

Vertikální škálování odkazuje na zvýšení nebo snížení výpočetní velikost jednotlivých databázových, označované také jako "vertikální navýšení kapacity."

Většina databáze aplikace v cloudovém měřítku pomocí kombinace těchto dvou strategií. Software jako služba aplikace může například použít horizontální škálování zřídit nové koncovým zákazníkům i vertikální škálování umožňující každého koncového uživatele databáze zvětšit nebo zmenšit prostředky podle potřeby úlohy.

* Horizontální škálování se spravuje pomocí [Klientská knihovna Elastic Database](sql-database-elastic-database-client-library.md).
* Vertikální škálování se provádí pomocí rutin prostředí Azure PowerShell Změna úrovně služby, nebo tak, že databáze v elastickém fondu.

## <a name="sharding"></a>Sharding
*Horizontální dělení* je technika, velké objemy dat, stejně jako strukturované distribuovat napříč několika databázemi, nezávislé. Obvykle se zejména s vytváření Software jako služba (SAAS) nabídky pro koncové zákazníky a podniky vývojářům pro cloud. Tyto koncové zákazníky se často označuje jako "tenanty". Horizontální dělení může být třeba z nejrůznějších důvodů:  

* Celkové množství dat je příliš velký, aby vyhovovaly omezením izolované databáze
* Propustnost transakcí celkové zatížení překračuje možnosti izolované databáze
* Tenanti můžou vyžadovat fyzickou izolaci od ostatních, tak samostatné databáze jsou potřeba pro každého klienta
* Různé části databáze může být potřeba se nacházejí v různých zeměpisných oblastech pro dodržování předpisů, výkonu nebo geopolitických důvodů.

U dalších scénářů, jako je například ingestování dat z distribuované zařízení je možné tak, aby vyplnil sadu databází, které jsou uspořádány časově horizontálního dělení. Například samostatné databáze může být vyhrazen pro každého dne nebo týdne. V takovém případě klíč horizontálního dělení může být celé číslo představující datum (k dispozici ve všech řádcích horizontálně dělené tabulky) a aplikací na podmnožinu databází, které pokrývá rozsah dotyčný musejí směrovat dotazy načítají se informace pro konkrétní období.

Horizontální dělení funguje nejlépe, když každá transakce v aplikaci může být omezena na jednu hodnotu klíče horizontálního dělení. Zajistí se tak, že všechny transakce jsou místní u konkrétní databáze.

## <a name="multi-tenant-and-single-tenant"></a>Více tenantů a jednoho tenanta
Některé aplikace používají nejjednodušším přístupem vytvořit samostatnou databázi pro každého tenanta. Tento přístup je **model horizontálního dělení jednoho tenanta** , které nabízí izolaci, možnost zálohování a obnovení a škálování na členitosti tenanta prostředků. S jedním tenantem horizontálního dělení Každá databáze je přidružená hodnota ID konkrétního tenanta (nebo hodnota klíče zákazníků), ale tento klíč nemusí být vždy k dispozici v samotných datech. Je zodpovědností aplikace směrovat každého požadavku k příslušné databázi – a to zjednodušit klientské knihovny.

![Srovnání s více tenanty jednoho tenanta][4]

Jiné scénáře společně pack více tenantů do databáze, místo izolace je do samostatné databáze. Tento model je typické **model horizontálního dělení víceklientské** – a mohou být řízeny skutečnost, že aplikace spravuje velkým počtem malých tenantů. Ve víceklientské horizontální dělení jsou všechny řádků v tabulkách databáze navržené přenášet klíč, který identifikuje ID tenanta nebo klíč horizontálního dělení. Znovu aplikační vrstva zodpovídá za směrování žádosti tenanta k příslušné databázi, a to může podporovat Klientská knihovna elastic database. Kromě toho lze použít zabezpečení na úrovní řádků do filtru řádků přístup každý klient – podrobnosti najdete v tématu [aplikací s více tenanty s nástroji elastic database a zabezpečení na úrovní řádků](sql-database-elastic-tools-multi-tenant-row-level-security.md). Opětovná distribuce dat mezi databázemi. může být potřeba s model horizontální dělení více tenantů a nástroj split-merge elastických databází usnadňují. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Přesun dat z několika databází tenantů jedním
Když vytváříte aplikaci SaaS, je typický a pomáhala potenciálním zákazníkům nabídnout zkušební verzi softwaru. V takovém případě je nákladově efektivní použití víceklientskou databází pro data. Jakmile potenciálního zákazníka zákazníka, databáze s jedním tenantem je však lepší vzhledem k tomu, že poskytuje lepší výkon. Pokud zákazník vytvořili dat během zkušebního období, použijte [nástroj split-merge](sql-database-elastic-scale-overview-split-and-merge.md) pro přesun dat z více tenantů do nové databáze s jedním tenantem.

## <a name="next-steps"></a>Další postup
Ukázková aplikace, které ukazuje klientskou knihovnu, naleznete v tématu [Začínáme s nástroji Elastic Database](sql-database-elastic-scale-get-started.md).

Převod existujících databází pomocí nástrojů, najdete v článku [migrace existujících databází pro horizontální navýšení kapacity](sql-database-elastic-convert-to-use-elastic-tools.md).

Specifika elastického fondu najdete v tématu [cenové a výkonové požadavky fondu elastické databáze](sql-database-elastic-pool.md), nebo vytvořte nový fond s [elastické fondy](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

