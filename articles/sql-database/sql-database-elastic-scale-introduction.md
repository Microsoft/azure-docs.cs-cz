---
title: Horizontální navýšení kapacity
description: Vývojáři softwaru jako služby (SaaS) mohou pomocí těchto nástrojů snadno vytvářet elastické a škálovatelné databáze v cloudu
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 44c6147402cbe05e62c091863cb0bd4f9235bfab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061650"
---
# <a name="scaling-out-with-azure-sql-database"></a>Horizontální navýšení kapacity s Azure SQL Database
Můžete snadno škálovat azure SQL databází pomocí **nástrojů elastické databáze.** Tyto nástroje a funkce umožňují používat databázové prostředky **Azure SQL Database** k vytváření řešení pro transakční úlohy a zejména software jako služba (SaaS) aplikace. Funkce elastické databáze se skládají z:

* [Klientská knihovna elastické databáze](sql-database-elastic-database-client-library.md): Klientská knihovna je funkce, která umožňuje vytvářet a udržovat databáze s oddíly.  Viz [Začínáme s nástroji elastické databáze](sql-database-elastic-scale-get-started.md).
* [Nástroj pro sloučení rozdělení elastické databáze](sql-database-elastic-scale-overview-split-and-merge.md): přesouvá data mezi rozdělenými databázemi. Tento nástroj je užitečný pro přesun dat z databáze s více klienty do databáze s jedním klientem (nebo naopak). Viz [Elastické databáze Split-Merge nástroj kurzu](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Úlohy elastické databáze:](elastic-jobs-overview.md)Ke správě velkého počtu databází Azure SQL použijte úlohy. Snadno provádět operace správy, jako jsou změny schématu, správa pověření, aktualizace referenčních dat, shromažďování dat o výkonu nebo shromažďování telemetrie tenanta (zákazníka) pomocí úloh.
* [Elastická databáze dotazu](sql-database-elastic-query-overview.md) (preview): Umožňuje spustit dotaz Transact-SQL, který zahrnuje více databází. To umožňuje připojení k nástrojům pro vytváření sestav, jako jsou Excel, Power BI, Tableau atd.
* [Elastické transakce](sql-database-elastic-transactions-overview.md): Tato funkce umožňuje spouštět transakce, které pokrývají několik databází v Azure SQL Database. Elastické databázové transakce jsou k dispozici pro aplikace .NET pomocí rozhraní ADO .NET a integrují se známým prostředím programování pomocí [tříd System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

Následující obrázek znázorňuje architekturu, která obsahuje **funkce elastické databáze** ve vztahu ke kolekci databází.

V této grafice představují schémata barvy databáze. Databáze se stejnou barvou sdílejí stejné schéma.

1. Sada **databází Azure SQL** je hostovaná v Azure pomocí architektury střepů.
2. Klientská **knihovna elastická databáze** se používá ke správě sady šiříku.
3. Podmnožina databází je vložena do **elastického fondu**. (Viz [Co je bazén?](sql-database-elastic-pool.md)).
4. Úloha **elastické databáze** spouští naplánované nebo ad hoc skripty T-SQL ve všech databázích.
5. **Nástroj rozdělení sloučení** se používá k přesunutí dat z jednoho úlomku do druhého.
6. **Elastické databáze dotaz** umožňuje napsat dotaz, který zahrnuje všechny databáze v sadě šiříku.
7. **Elastické transakce** umožňují spouštět transakce, které pokrývají několik databází. 

![Nástroje pro elastické databáze][1]

## <a name="why-use-the-tools"></a>Proč používat nástroje?
Dosažení elasticity a škálování pro cloudové aplikace bylo pro virtuální zařízení a úložiště objektů blob jednoduché – jednoduše přidejte nebo odečtěte jednotky nebo zvyšte výkon. Ale to zůstalo výzvou pro stavové zpracování dat v relačních databázích. Výzvy se objevily v těchto scénářích:

* Rostoucí a zmenšující se kapacita pro relační databázi část vašeho pracovního vytížení.
* Správa aktivních bodů, které mohou vzniknout ovlivňující určitou podmnožinu dat – například zaneprázdněného koncového zákazníka (tenanta).

Tradičně scénáře, jako jsou tyto byly řešeny investováním do většího měřítku databázových serverů pro podporu aplikace. Tato možnost je však omezena v cloudu, kde veškeré zpracování probíhá na předdefinovaném komoditním hardwaru. Místo toho distribuce dat a zpracování v mnoha identicky strukturovaných databázích (vzor horizontálního navýšení kapacity označovaný jako "horizontálního oddílu") poskytuje alternativu k tradičním přístupům škálování jak z hlediska nákladů, tak z hlediska elasticity.

## <a name="horizontal-and-vertical-scaling"></a>Vodorovné a svislé měřítko
Následující obrázek znázorňuje vodorovné a svislé rozměry změny měřítka, což jsou základní způsoby, jak lze změnit velikost elastických databází.

![Horizontální versus vertikální horizontální horizontální horizontální škálování][2]

Horizontální škálování odkazuje na přidání nebo odebrání databází za účelem úpravy kapacity nebo celkového výkonu, nazývaného také "škálování". Horizontálního dělení, ve kterém jsou data rozdělena napříč kolekcí identicky strukturovaných databází, je běžný způsob implementace horizontálníškálování.  

Vertikální škálování odkazuje na zvýšení nebo snížení výpočetní velikosti jednotlivé databáze, označované také jako "škálování nahoru."

Většina databázových aplikací v cloudovém měřítku používá kombinaci těchto dvou strategií. Například software jako služba aplikace může použít horizontální škálování zřídit nové koncové zákazníky a vertikální škálování povolit každý koncový zákazník databáze pro růst nebo zmenšit prostředky podle potřeby zatížení.

* Horizontální škálování je spravováno pomocí [klientské knihovny elastická databáze](sql-database-elastic-database-client-library.md).
* Vertikální škálování se provádí pomocí rutin Azure PowerShell změnit úroveň služby nebo umístěním databází v elastickém fondu.

## <a name="sharding"></a>Sharding
*Horizontální dělení* je technika distribuce velkých objemů dat, která mají stejnou strukturu, mezi více nezávislých databází. To je obzvláště populární u vývojářů cloudu, kteří vytvářejí nabídky Softwaru jako služby (SAAS) pro koncové zákazníky nebo firmy. Tito koncoví zákazníci jsou často označováni jako "tenanti". Sharding může být vyžadován z libovolného počtu důvodů:  

* Celkové množství dat je příliš velké a nevejde se do omezení jednotlivých databází.
* Propustnost transakce celkového pracovního vytížení překračuje možnosti jednotlivých databází.
* Klienti mohou vyžadovat fyzickou izolaci od sebe navzájem, takže jsou potřeba samostatné databáze pro každého klienta
* Různé části databáze může být nutné kin v různých zeměpisných oblastech z důvodu dodržování předpisů, výkonu nebo geopolitických důvodů.

V jiných scénářích, jako je například požití dat z distribuovaných zařízení, rozdělení se tvrdostu lze vyplnit sadu databází, které jsou uspořádány časově. Například samostatná databáze může být vyhrazena pro každý den nebo týden. V takovém případě může být klíč dělení celé číslo představující datum (ve všech řádcích tabulky s rozdělením) a dotazy načítající informace pro rozsah dat musí být směrovány aplikací do podmnožiny databází pokrývajících daný rozsah.

Sharding funguje nejlépe, když každá transakce v aplikaci může být omezena na jednu hodnotu klíče s ráždí. To zajišťuje, že všechny transakce jsou místní do konkrétní databáze.

## <a name="multi-tenant-and-single-tenant"></a>Víceklientů a jednoklient
Některé aplikace používají nejjednodušší přístup k vytvoření samostatné databáze pro každého klienta. Tento přístup je **vzor horizontálního oddílu jednoho klienta,** který poskytuje izolaci, schopnost zálohování a obnovení a škálování prostředků na rozlišovací schopnost klienta. S jedním klientem syničů je každá databáze přidružena k určité hodnotě ID klienta (nebo hodnotě klíče zákazníka), ale tento klíč nemusí být vždy přítomen v samotných datech. Je odpovědností aplikace směrovat každý požadavek do příslušné databáze - a klientská knihovna to může zjednodušit.

![Jeden tenant versus více klientů][4]

Jiné scénáře zabalit více klientů společně do databází, spíše než jejich izolace do samostatných databází. Tento vzor je typický **víceklientský vzor syrových oborů** – a může být řízen skutečností, že aplikace spravuje velký počet malých klientů. Ve víceklientské syrový oborování řádky v databázových tabulkách jsou navrženy tak, aby nést klíč identifikující ID klienta nebo syrový klíč. Opět platí, že aplikační vrstva je zodpovědná za směrování požadavku klienta do příslušné databáze a to může být podporováno klientskou knihovnou elastické databáze. Kromě toho zabezpečení na úrovni řádků lze filtrovat, které řádky každý klient přístup – podrobnosti naleznete v [tématu víceklientské aplikace s elastické databázové nástroje a zabezpečení na úrovni řádků](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuce dat mezi databázemi může být potřeba s víceklientské rozdělení vzor a je usnadněno elastické databáze split-merge nástroj. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Přesunutí dat z více databází do databází s jedním nájemem
Při vytváření aplikace SaaS je typické nabídnout potenciálním zákazníkům zkušební verzi softwaru. V tomto případě je nákladově efektivní použít víceklientské databáze pro data. Pokud se však potenciální zákazník stane zákazníkem, je databáze s jedním tenantem lepší, protože poskytuje lepší výkon. Pokud zákazník vytvořil data během zkušebního období, použijte [nástroj rozdělení sloučení](sql-database-elastic-scale-overview-split-and-merge.md) přesunout data z více klienta do nové databáze jednoho klienta.

## <a name="next-steps"></a>Další kroky
Ukázková aplikace, která ukazuje klientskou knihovnu, najdete v [tématu Začínáme s nástroji elastické databáze](sql-database-elastic-scale-get-started.md).

Chcete-li převést existující databáze na použití nástrojů, přečtěte si informace [o migraci existujících databází, abyste je navýšili](sql-database-elastic-convert-to-use-elastic-tools.md).

Chcete-li zobrazit specifika elastického fondu, přečtěte si informace [o ceně a výkonu pro elastický fond](sql-database-elastic-pool.md)nebo vytvořte nový fond s [elastické fondy](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

