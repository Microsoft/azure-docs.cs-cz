---
title: Horizontální navýšení kapacity s Azure SQL Database | Microsoft Docs
description: Software jako služba (SaaS) vývojáři můžete snadno vytvořit elastické, škálovatelná databáze v cloudu pomocí těchto nástrojů
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: e8a07f1a1d172a96646a5c71a6267239e159c267
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646564"
---
# <a name="scaling-out-with-azure-sql-database"></a>Horizontální navýšení kapacity s Azure SQL Database
Můžete snadno škálovat databáze Azure SQL pomocí **elastické databáze** nástroje. Tyto nástroje a funkce, budete moct použít databázi prostředků **Azure SQL Database** vytvářet řešení pro transakční zatížení a hlavně Software jako služba (SaaS) aplikace. Funkce elastické databáze se skládají z:

* [Klientská knihovna pro elastické databáze](sql-database-elastic-database-client-library.md): klientské knihovny je funkce, která vám umožní vytvořit a udržovat horizontálně dělené databáze.  V tématu [začít pracovat s nástroji elastické databáze](sql-database-elastic-scale-get-started.md).
* [Nástroj rozdělení sloučení elastické databáze](sql-database-elastic-scale-overview-split-and-merge.md): přesouvá data mezi horizontálně dělené databáze. To je užitečné pro přesun dat z databáze víceklientské databáze jednoho klienta (nebo naopak). V tématu [kurzu nástroje elastické databáze rozdělení sloučení](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Elastické databáze úlohy](sql-database-elastic-jobs-overview.md) (preview): použití úlohy pro správu velkého počtu databází Azure SQL. Snadno proveďte operace správy, například změny schématu, Správa přihlašovacích údajů, aktualizace dat odkaz, shromažďování dat výkonu nebo pomocí úlohy kolekce telemetrie klienta (zákazníka).
* [Elastické databáze dotazu](sql-database-elastic-query-overview.md) (preview): umožňuje spuštění dotazu jazyka Transact-SQL, která přesahuje více databází. To umožňuje připojení k vytváření sestav nástroje, jako je aplikace Excel, PowerBI, Tableau, atd.
* [Elastické transakce](sql-database-elastic-transactions-overview.md): Tato funkce umožňuje spustit transakce, které jsou rozmístěny v několika databází v Azure SQL Database. Transakce elastické databáze jsou k dispozici pro aplikace .NET pomocí rozhraní ADO .NET a integrovat známé programování pomocí prostředí [System.Transaction třídy](https://msdn.microsoft.com/library/system.transactions.aspx).

Následující obrázek znázorňuje architekturu, která zahrnuje **elastické databáze funkce** ve vztahu k kolekce databáze.

V této grafiky představují barvy databáze schémat. Databáze s stejné barvy sdílet stejné schéma.

1. Sadu **databází Azure SQL** jsou hostované v Azure pomocí architektury horizontálního dělení.
2. **Klientské knihovny pro elastické databáze** slouží ke správě sadu horizontálního oddílu.
3. Podmnožinu databází jsou vloženy do **elastický fond**. (Viz [co je fond?](sql-database-elastic-pool.md)).
4. **Úlohy elastické databáze** spustí naplánované nebo ad hoc skriptů T-SQL pro všechny databáze.
5. **Nástroji pro sloučení rozdělení** se používá pro přesun dat z jedné horizontálního oddílu do jiného.
6. **Elastické databáze dotazu** umožňuje vytvořit dotaz, který zahrnuje všechny databáze v sadě horizontálního oddílu.
7. **Elastické transakce** vám umožní spustit transakce, které jsou rozmístěny v několika databází. 

![Nástroje pro elastické databáze][1]

## <a name="why-use-the-tools"></a>Proč používat nástroje?
Dosažení pružnost a škálování pro cloudové aplikace byla přehledné pro virtuální počítače a úložiště objektů blob – jednoduše přidat odečtena jednotek nebo zvýšení výkonu. Ale zůstal výzvu pro zpracování stavových dat v relačních databází. Výzvy vyplývá v těchto scénářích:

* Zvětšování a zmenšování kapacity pro relační databázi část vašich úloh.
* Správa aktivní body, které může způsobit, které mají vliv na určitou podmnožinu dat – například zaneprázdněn end zákazníků (klientů).

Obvyklým scénáře takovéto mít vyřeší investicemi v serverech databáze větší škálování k podpoře aplikace. Tato možnost je však omezená v cloudu, kde se stane veškeré zpracování na předdefinované komoditním hardwaru. Místo toho distribuci dat a zpracování mezi mnoha databázemi stejně jako strukturovaný (známé jako "horizontálního dělení" vzor Škálováním na více systémů) představuje alternativu ke tradiční přístupy škálování z hlediska nákladů a pružnost.

## <a name="horizontal-and-vertical-scaling"></a>Vodorovného a svislého škálování
Následující obrázek znázorňuje vodorovného a svislého rozměry škálování, které jsou základní způsoby, které je možné rozšířit elastické databáze.

![Vodorovný versus svislé škálování][2]

Vodorovné škálování odkazuje na přidáním nebo odebráním databází, aby bylo možné upravit kapacity nebo celkový výkon. To je také označován "škálování" out. Horizontálního dělení, ve kterém jsou data rozdělena v kolekci stejně jako strukturovaný databází, je běžné způsob, jak implementovat vodorovné škálování.  

Svislé škálování odkazuje zvýšením nebo snížením úrovně výkonu jednotlivé databáze – to se také označuje jako "vertikálním navýšení kapacity."

Většina databázových aplikací cloudového škálovatelného použít kombinaci těchto dvou strategií. Software jako služba aplikace může například použít vodorovné škálování zřídit nové koncové zákazníky a svislé škálování aby zvětšovat a zmenšovat prostředky podle potřeby úlohy databázi každého koncoví zákazníka.

* Vodorovné škálování je spravovat pomocí [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md).
* Svislé škálování se provádí pomocí rutin prostředí Azure PowerShell Pokud chcete změnit úroveň služby, nebo umístění databází v elastickém fondu.

## <a name="sharding"></a>Sharding
*Horizontálního dělení* jde o techniku distribuovat velkých objemů stejně jako strukturovaná data mezi několik nezávislých databáze. Jde zejména oblíbených s vývojáři cloudu vytváření Software jako služba (SAAS) nabídky pro koncové zákazníky nebo firmy. Tyto koncové zákazníky se často označuje jako "klienty". Horizontálního dělení může být nutný pro libovolný počet důvodů:  

* Celkové množství dat je příliš velký, aby vyhovovaly limitu omezení služby jedné databáze
* Transakce propustnost celkové zatížení přesáhne možnosti služby jedné databáze
* Klienti mohou vyžadovat fyzické izolace od sebe navzájem tak samostatné databáze jsou potřeba pro každého klienta
* Různé části databáze potřebovat jsou umístěny v různých zeměpisných regionech pro dodržování předpisů, výkon nebo geopolitické důvodů.

V dalších scénářích, třeba přijímání dat z distribuované zařízení může být horizontálního dělení použitou k vyplnění sadu databází, která jsou uspořádána dočasně. Například samostatné databáze může být vyhrazený pro každého dne nebo týdne. V takovém případě klíč horizontálního dělení může být celé číslo představující datum (součástí všechny řádky horizontálně dělené tabulky) a aplikací na podmnožinu databází vztahující se konkrétní oblast musejí směrovat dotazy načítají se informace o pro konkrétní období.

Horizontálního dělení je nejvhodnější při každé transakci v aplikaci může být omezen na jednu hodnotu klíče horizontálního dělení. Která zajistí, že všechny transakce jsou místní pro konkrétní databázi.

## <a name="multi-tenant-and-single-tenant"></a>Více klientů a jednoho klienta
Některé aplikace použít nejjednodušší způsob vytváření samostatnou databázi pro každého klienta. Toto je **jednoho klienta horizontálního dělení vzor** izolace, možnost zálohování a obnovení a prostředků, škálování na členitost klienta, který poskytuje. S horizontálního dělení jednoho klienta je přidružená hodnota ID konkrétní klienta (nebo hodnota klíče zákazníka) každou databázi, ale klíči nemusí být vždy přítomen v samotná data. Je zodpovědností aplikace směrovat každý požadavek na příslušnou databázi - a klientské knihovny můžete zjednodušit tím.

![Jednoho klienta a více klientů][4]

Jiné scénáře pack víc klientů současně do databáze, místo izoluje je do samostatné databáze. Toto je typické **horizontálního dělení víceklientské vzor** - a mohou být řízeny fakt, že aplikaci spravuje velké množství malých klientů. V víceklientské horizontálního dělení jsou všechny řádků v tabulkách databáze navrženy pro provádění klíč, který identifikuje ID klienta nebo klíč horizontálního dělení. Znovu aplikační vrstvě je zodpovědná za směrování požadavek klienta na příslušnou databázi, a to může podporovat klientské knihovny pro elastické databáze. Kromě toho zabezpečení na úrovni řádků slouží k filtru řádky k přístup každý klient – podrobnosti najdete v tématu [víceklientským aplikacím s nástroje elastické databáze a zabezpečení na úrovni řádků](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuce data mezi databáze může být potřeba pomocí vzoru horizontálního dělení více klientů, a to usnadňují nástrojem rozdělení sloučení elastické databáze. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Přesun dat z více k databázím jedním klientů
Při vytváření aplikace SaaS, je obvykle nabízejí potenciální zákazníci zkušební verzi softwaru. V takovém případě je nákladově efektivní používání víceklientské databáze pro data. Když se stane potenciálního zákazníka zákazníka, databázi jednoho klienta je nicméně lepší vzhledem k tomu, že poskytuje lepší výkon. Pokud zákazník vytvořili dat během zkušební doby, použijte [nástroji pro sloučení rozdělení](sql-database-elastic-scale-overview-split-and-merge.md) pro přesun dat z více klientů k nové databázi jednoho klienta.

## <a name="next-steps"></a>Další postup
Ukázkovou aplikaci, která demonstruje klientské knihovny, najdete v části [začít pracovat s nástroji elastické databáze](sql-database-elastic-scale-get-started.md).

Chcete-li převést existující databáze používat nástroje, přečtěte si téma [migrovat existující databáze pro rozšíření Škálováním](sql-database-elastic-convert-to-use-elastic-tools.md).

Specifika elastického fondu, najdete v sekci [cenové a výkonové požadavky fondu elastické databáze](sql-database-elastic-pool.md), nebo vytvořit nový fond s [elastické fondy](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

