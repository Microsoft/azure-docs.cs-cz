---
title: Azure SQL Database modely nákupu | Dokumentace Microsoftu
description: Další informace o tyto modely nákupu, které jsou k dispozici pro službu Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431368"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Výběr mezi virtuálních jader a jednotek DTU zakoupení modelů

Azure SQL Database vám umožňuje snadný nákup plně spravovaná platforma jako služba (PaaS) databázový stroj, který nejlépe vyhovuje vašim potřebám výkonu a nákladů. V závislosti na modelu nasazení, který jste zvolili pro službu Azure SQL Database můžete vybrat nákupní model, který vám vyhovuje:

- [Virtuální jádro (vCore) – nákupní model založený na](sql-database-service-tiers-vcore.md) (doporučeno). Tento nákupní model poskytuje možnost volby mezi vrstvou zřízených výpočetních a výpočetní vrstva bez serveru (preview). Úroveň zřízených výpočetních zvolte přesné množství výpočetních prostředků, které jsou vždy zřízené pro vaši úlohu. Na úrovni výpočetní prostředí zadejte automatické škálování výpočetních prostředků v rozsahu konfigurovatelné výpočetní prostředky. V rámci této vrstvy výpočetní prostředky můžete také automaticky pozastavit a obnovit databáze na základě pracovního vytížení aktivity. VCore Jednotková cena za jednotku doby je nižší úrovni zřízených výpočetních, než je na úrovni výpočetní prostředí.
- [Jednotky transakcí databáze (DTU) – nákupní model založený na](sql-database-service-tiers-dtu.md). Tento nákupní model poskytuje jako součást balíčku výpočetní výkon a úložiště balíčků pro běžné úlohy s vyrovnáváním.

Tyto modely nákupu různých jsou k dispozici pro různé modely nasazení Azure SQL Database:

- [Izolované databáze](sql-database-single-databases-manage.md) a [elastického fondu](sql-database-elastic-pool.md) možností nasazení v [Azure SQL Database](sql-database-technical-overview.md) nabízí i [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
- [Spravovanou instanci](sql-database-managed-instance.md) možnost nasazení ve službě Azure SQL Database nabízí jen [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
- [Úroveň služby hyperškálovatelného](sql-database-service-tier-hyperscale.md) je dostupné pro izolované databáze, které používáte [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

Následující tabulku a graf porovnání a kontrastu základě virtuálních jader a založený na DTU tyto modely nákupu:

|**Nákupní model**|**Popis**|**Nejlepší pro**|
|---|---|---|
|Model na základě DTU|Tento model je založen na připojené míra prostředků pro výpočty, úložiště a vstupně-výstupních operací. Velikosti výpočetních jsou vyjádřeny v jednotkách Dtu pro izolované databáze a jednotky transakcí elastické databáze (Edtu) pro elastické fondy. Další informace o jednotkách Dtu a Edtu najdete v tématu [co jsou jednotky Dtu a Edtu?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Nejvhodnější pro zákazníky, kteří chtějí možnosti jednoduché, předkonfigurované prostředku.|
|Model na základě virtuálních jader|Tento model umožňuje zvolit nezávisle na sobě výpočetní a úložnou kapacitu. Nákupní model založený na virtuálních jádrech také umožňuje používat [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) získat úspory nákladů.|Nejvhodnější pro zákazníky, kteří hodnota flexibilitu, řízení a transparentnost.|
||||  

![porovnání cenový model](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Náklady na výpočetní výkon

### <a name="provisioned-compute-costs"></a>Zřízených výpočetních nákladů

Náklady na výpočetní prostředky na úrovni zřízených výpočetních odráží celkové výpočetní kapacitu, pro kterého je zřízené pro aplikaci.

V obchodní vrstvě důležité služby můžeme automaticky přidělit nejméně 3 repliky. Tak, aby odrážela tuto další přidělování výpočetních prostředků, cena v nákupní model založený na virtuálních jádrech je přibližně 2.7 x vyšší v obchodní vrstvě důležité služby, než je v rámci úrovně služeb pro obecné účely. Obdobně vyšší úložiště cen za GB ve vrstvě služeb kritické obchodní odráží vysoké vstupně-výstupních operací a úložiště SSD s nízkou latencí.

Náklady na úložiště zálohování je stejný pro obchodní vrstvu služby a služby úrovni general purpose, protože obě vrstvy používají úložiště úrovně standard.

### <a name="serverless-compute-costs"></a>Náklady na výpočetní prostředí

Popis jak definuje výpočetní kapacitu a náklady se počítají na úrovni výpočetní prostředí, najdete v části [bez serveru SQL Database (preview)](sql-database-serverless.md).

## <a name="storage-costs"></a>Cena za uložení

Různé typy úložiště se účtují různě. Pro ukládání dat vám budeme účtovat zřízeného úložiště na základě maximální velikost databáze nebo fondu, které vyberete. Náklady nemění, není-li snížit nebo zvýšit maximální. Úložiště zálohování je spojen s automatickým zálohám vaší instance a dynamicky přidělit. Prodloužení doby uchovávání zálohování zvyšuje požadavky na záložní úložiště je využívána vaší instance.

Ve výchozím nastavení 7 dní automatizovaných záloh vašich databází se zkopírují do geograficky redundantního úložiště jen pro čtení (RA-GRS) účtu standardní úložiště objektů Blob. Toto úložiště využívané týdenními úplnými zálohami, denními rozdílovými zálohami a zálohování protokolů transakcí, které se kopírují každých 5 minut. Velikosti protokolů transakcí závisí na četnosti změn databáze. Minimální objem úložiště velikosti 100 % velikosti databáze, je k dispozici bez dalších poplatků. Využití úložiště zálohování je účtovat GB za měsíc.

Další informace o cenách úložiště, najdete v článku [ceny](https://azure.microsoft.com/pricing/details/sql-database/single/) stránky.

## <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

Virtuální jádro (vCore) reprezentuje logický procesor a nabízí možnost volby mezi generacemi hardwaru a fyzické charakteristiky hardwaru (například počet jader, paměti a velikost úložiště). Nákupní model založený na virtuálních jádrech poskytuje vám flexibilitu, kontrolu, transparentnost spotřeby jednotlivých prostředků, a jednoduchý způsob převodu místních požadavků na zatížení do cloudu. Tento model umožňuje zvolit výpočetních, paměťových a úložiště prostředků na základě potřeb vašich úloh.

V nákupní model založený na virtuálních jádrech můžete vybrat mezi [Obecné](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) a [pro důležité obchodní informace](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) úrovně u služeb [izolované databáze](sql-database-single-database-scale.md), [ elastické fondy](sql-database-elastic-pool.md), a [spravované instance](sql-database-managed-instance.md). Pro izolované databáze, můžete také [úroveň služby hyperškálovatelného](sql-database-service-tier-hyperscale.md).

Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě zvolte výpočetních a úložných kapacit, vyhledat zajištění místního výkonu a optimalizovat cena. V nákupní model založený na virtuálních jádrech platíte za:

- Výpočetní prostředky (na úrovni služby a počet virtuálních jader a množství paměti a jeho generaci).
- Typ a velikost úložiště dat a protokolů.
- Záložní úložiště (pro čtení RA-GRS).

> [!IMPORTANT]
> Výpočetní prostředky, vstupně-výstupní operace a úložiště dat a protokolů se účtují na databázi nebo elastický fond. Úložiště záloh se účtuje za každou databázi. Další informace o poplatcích spravované instance najdete v tématu [spravované instance](sql-database-managed-instance.md).
> **Omezení oblasti:** Aktuální seznam podporovaných oblastí najdete v tématu [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Chcete-li vytvořit spravovanou instanci v oblasti, kterou aktuálně nepodporuje [odeslat žádost o podporu prostřednictvím webu Azure portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Pokud se elastický fond nebo izolovanou databázi spotřebovává více než 300 Dtu, může snížit náklady a převod na nákupní model založený na virtuálních jádrech. Můžete převést pomocí rozhraní API podle výběru nebo pomocí webu Azure portal bez výpadků. Převod však není nutné a neprovede automaticky. Nákupní model založený na DTU splňuje výkonu a obchodních požadavcích, měli byste pokračovat, jeho použití.

Převést z nákupní model založený na DTU pro nákupní model založený na virtuálních jádrech, vyberte pomocí následující hrubé odhady výpočetního prostředí:

- Každých 100 Dtu na úrovni standard vyžadovat alespoň 1 virtuální jádro v rámci úrovně služeb pro obecné účely.
- Každý 125 Dtu na úrovni premium vyžadují aspoň 1 virtuální jádro v obchodní vrstvě důležité služby.

## <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

Jednotky transakce databáze (DTU) představuje kombinaci měření procesoru, paměti, čtení a zápisu. Nákupní model založený na DTU nabízí sadu předem nakonfigurované sady výpočetních prostředků a zahrnuté úložiště na jednotce různé úrovně výkonu aplikace. Pokud dáváte přednost jednoduchosti, kterou nabízejí předem nakonfigurované sady prostředků a pevných plateb každý měsíc, model založený na DTU pravděpodobně vhodnější pro vaše potřeby.

V nákupní model založený na DTU můžete vybrat mezi úrovněmi basic, standard a úrovně služeb premium pro obě [izolované databáze](sql-database-single-database-scale.md) a [elastické fondy](sql-database-elastic-pool.md). Nákupní model založený na DTU není k dispozici pro [spravované instance](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Jednotky transakcí databáze (Dtu)

Pro jednu databázi na daném vypočítat velikost v rámci [úroveň služby](sql-database-single-database-scale.md), Microsoft zaručuje určité úrovně prostředků pro tuto databázi (nezávisle na jakékoli jiné databáze v cloudu Azure). Tuto záruku poskytuje předvídatelný úroveň výkonu. Množství prostředků přidělených pro databázi se vypočte takto: počet Dtu a je určena jako součást balíčku prostředků pro výpočty, úložiště a vstupně-výstupních operací.

Je poměr mezi tyto prostředky původně určena [úlohou srovnávacího testu online zpracování transakcí (OLTP)](sql-database-benchmark-overview.md) navržena jako typická pro úlohy OLTP v reálném světě. Pokud vaše úloha překračuje množství některý z těchto prostředků, se omezuje propustnost, výsledkem je pomalejší výkon a časové limity.

Prostředky využívané třídou úlohy nebudou mít vliv na prostředky dostupné pro jiné databáze SQL v cloudu Azure. Obdobně prostředky využívané třídou jiné úlohy nebudou mít vliv na prostředky dostupné pro vaši službu SQL database.

![ohraničujícího rámečku](./media/sql-database-what-is-a-dtu/bounding-box.png)

Počet jednotek Dtu jsou zvláště užitečná pro pochopení relativní prostředky, které jsou přiděleny pro Azure SQL Database v různých velikostech výpočetních prostředků a úrovní služeb. Příklad:

- Zdvojnásobení jednotek Dtu zvýšením výpočetní velikost databáze odpovídá zdvojnásobení sady prostředků, které jsou k dispozici pro tuto databázi.
- Databáze úrovně premium služby úrovně P11 se 1 750 Dtu nabízí 350 x více DTU výpočetní výkon než databázi úroveň služeb basic s 5 Dtu.  

Chcete-li získat podrobnější přehled o spotřebě prostředků (DTU) vašich úloh, použijte [insights výkon dotazů](sql-database-query-performance.md) na:

- Identifikujte hlavní dotazy podle počtu CPU/doba trvání/spuštění, který může být potenciálně vyladěný za účelem vylepšení výkonu. Například by mohlo prospět dotazu I vstupně-výstupními operacemi [techniky optimalizace v paměti](sql-database-in-memory.md) lepší využití paměti k dispozici na určitou úroveň služby a vypočítat velikost.
- Procházet podrobnosti dotazu pro zobrazení jeho textu a její historii využití prostředků.
- Přístup k doporučení pro vyladění výkonu, které ukazují akce provedené [služby SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednotky transakcí elastické databáze (Edtu)

Pro databáze SQL, které jsou vždy k dispozici, spíše než poskytuje vyhrazenou sadu prostředků (Dtu), které nemusí být vždy nezbytné, můžete umístit tyto databáze do [elastického fondu](sql-database-elastic-pool.md). Databáze v elastickém fondu jsou na jednom serveru Azure SQL Database a sdílejí fond zdrojů.

Sdílené prostředky v elastickém fondu jsou měřeny pomocí jednotky transakcí elastické databáze (Edtu). Elastické fondy poskytují jednoduché a nákladově efektivní řešení správy výkonnostních cílů pro více databází, které mají často proměnlivé a nepředvídatelné vzorce používání. Elastický fond zaručuje, že všechny prostředky nemůže být zpracován jedna databáze ve fondu, přitom zajistit, že každá databáze ve fondu má vždy minimální množství potřebné prostředky, které jsou k dispozici.

Fond je uveden stanovený počet jednotek Edtu za stanovenou cenu. Jednotlivé databáze v elastickém fondu, může automatické škálování v rámci nakonfigurovaných mezí. Databáze při větším zatížení bude spotřebovávat více Edtu, které mají pokrýt poptávku. Databáze pod označením zatížení se spotřebovávají méně Edtu. Databáze s žádné zatížení bude nespotřebovávají žádné Edtu. Protože prostředky zřídí pro celý fond, spíše než jednotlivé databáze, elastické fondy úkoly správy zjednodušují a poskytují předvídatelný rozpočet pro fond.

Další Edtu lze přidat do existujícího fondu bez jakéhokoli výpadku databáze a bez jakéhokoli dopadu na databáze ve fondu. Podobně pokud potřebujete již přidané Edtu, je z existujícího fondu kdykoli odeberte. Můžete také přidat databáze nebo odečíst databáze z fondu kdykoli. A vyhradit tak Edtu pro ostatní databáze, omezte počet jednotek Edtu, které databázi můžete použít v případě velkého zatížení. Pokud se databáze trvale underuses prostředky, ji odebrat z fondu a nakonfigurovat jako izolovanou databázi s předvídatelným množstvím požadovaných prostředků.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Určit počet Dtu potřebný podle úloh

Pokud chcete migrovat existující místní nebo úloha virtuálního počítače SQL serveru do služby Azure SQL Database, použijte [kalkulačku DTU](https://dtucalculator.azurewebsites.net/) k odhadu počtu potřebných Dtu. Pro existující úlohy Azure SQL Database, použijte [insights výkon dotazů](sql-database-query-performance.md) porozumět využití prostředků databáze (Dtu) a získat podrobnější přehled pro optimalizaci vašich úloh. [Sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) zobrazení dynamické správy (DMV) umožňuje zobrazit využití prostředků za poslední hodinu. [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) katalogu zobrazení využití prostředků za posledních 14 dní, ale v méně přesné průměry pět minut.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Úlohy, které využívají samosprávné elastického fondu prostředků

Fondy jsou vhodné pro databáze s průměr nízké využití prostředků a poměrně málo četné špičky využití. SQL Database automaticky vyhodnotí historické údaje používání prostředků databází na existujícím serveru SQL Database a doporučí odpovídající konfigurace fondu na webu Azure Portal. Další informace najdete v tématu [při měli byste zvážit elastického fondu SQL Database?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Je nutné nastavit aplikaci offline převést z úrovně služeb na základě DTU pro vrstvu služby založený na virtuálních jádrech?

Ne. Není nutné převést aplikaci do režimu offline. Nové úrovně služeb nabízejí jednoduchý způsob online převodu, který je podobný stávajícího procesu upgradu databází z standardní úrovně premium a naopak. Můžete spustit tento převod pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, T-SQL nebo rozhraní REST API. Zobrazit [Správa izolovaných databází](sql-database-single-database-scale.md) a [Správa elastických fondů](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Můžu převést databáze z úrovně služeb v nákupní model založený na virtuálních jádrech pro vrstvu služby v nákupní model založený na DTU?

Ano, můžete snadno převést databázi do jakékoli objective podporované výkonu pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, T-SQL nebo rozhraní REST API. Zobrazit [Správa izolovaných databází](sql-database-single-database-scale.md) a [Správa elastických fondů](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Další postup

- Další informace o nákupní model založený na virtuálních jádrech najdete v tématu [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
- Další informace o nákupní model založený na DTU najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
