---
title: Nákupní modely
description: Přečtěte si o nákupních modelech, které jsou dostupné pro Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255987"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Vyberte si mezi virtuálním jádrem a nákupními modely DTU

Azure SQL Database vám umožní snadno zakoupit plně spravovanou platformu jako databázový stroj služby (PaaS), který vyhovuje vašim potřebám výkonu a nákladů. V závislosti na modelu nasazení, který jste vybrali pro Azure SQL Database, můžete vybrat nákupní model, který vám vyhovuje:

- [Nákupní model založený na virtuálním jádru (virtuální jádro)](sql-database-service-tiers-vcore.md) (doporučeno). Tento nákupní model poskytuje možnost volby mezi zřízenou výpočetní vrstvou a výpočetní vrstvou bez serveru. S zřízenou výpočetní vrstvou zvolíte přesné množství výpočetních prostředků, které jsou vždy zřízené pro vaše úlohy. S výpočetní vrstvou bez serveru zadáte automatické škálování výpočetních prostředků přes konfigurovatelný výpočetní rozsah. Pomocí této výpočetní vrstvy můžete také automaticky pozastavit a obnovit databázi na základě aktivity pracovního vytížení. Jednotková cena virtuálního jádra za jednotku času je nižší v zřízené výpočetní vrstvě než ve výpočetní vrstvě bez serveru.
- [Nákupní model založený na databázové transakční jednotce (DTU)](sql-database-service-tiers-dtu.md). Tento nákupní model poskytuje balíčky výpočetních prostředků a úložiště vyvážené pro běžné úlohy.

Různé nákupní modely jsou k dispozici pro různé modely nasazení Azure SQL Database:

- Možnosti nasazení [jednotné databáze](sql-database-single-databases-manage.md) a [elastického fondu](sql-database-elastic-pool.md) v Azure SQL [Database](sql-database-technical-overview.md) nabízejí nákupní model založený [na DTU](sql-database-service-tiers-dtu.md) i [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
- Možnost nasazení [spravované instance](sql-database-managed-instance.md) v Azure SQL Database nabízí jenom nákupní model založený [na virtuálních jádrech](sql-database-service-tiers-vcore.md).
- [Úroveň služby Hyperscale](sql-database-service-tier-hyperscale.md) je k dispozici pro jednotlivé databáze, které používají [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

Následující tabulka a graf porovnávají a kontrastují nákupní modely založené na virtuálních jádrech a na dtu:

|**Nákupní model**|**Popis**|**Nejlepší pro**|
|---|---|---|
|Model na základě DTU|Tento model je založen na přivázané maješce výpočetních, úložných a vstupně-no prostředků. Výpočetní velikosti jsou vyjádřeny v DTU pro jednotlivé databáze a v jednotkách transakce elastické databáze (eDTU) pro elastické fondy. Další informace o dtu a eDTU naleznete v tématu [Co jsou DTU a eDTU?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Nejlepší pro zákazníky, kteří chtějí jednoduché, předkonfigurované možnosti prostředků.|
|Model na základě virtuálních jader|Tento model umožňuje nezávisle zvolit výpočetní prostředky a prostředky úložiště. Nákupní model založený na virtuálních jádrech také umožňuje používat [hybridní výhody Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) k dosažení úspor nákladů.|Nejlepší pro zákazníky, kteří oceňují flexibilitu, kontrolu a transparentnost.|
||||  

![porovnání cenových modelů](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Výpočetní náklady

### <a name="provisioned-compute-costs"></a>Zřízené výpočetní náklady

V zřízené výpočetní vrstvě výpočetní náklady odráží celkovou výpočetní kapacitu, která je zřízená pro aplikaci.

Ve vrstvě služby Business Critical automaticky přidělíme alespoň 3 repliky. Aby se zohlednilo toto dodatečné přidělení výpočetních prostředků, cena v nákupním modelu založeném na virtuálních jádrech je přibližně 2,7x vyšší na úrovni služby Business Critical než na úrovni služeb pro obecné účely. Stejně tak vyšší cena úložiště za GB na úrovni služby Business Critical odráží vyšší limity vstupně-výstupních služeb a nižší latenci úložiště SSD.

Náklady na úložiště záloh jsou stejné pro úroveň služby Business Critical a úroveň služby obecné účely, protože obě vrstvy používají standardní úložiště pro zálohování.

### <a name="serverless-compute-costs"></a>Výpočetní náklady bez serveru

Popis způsobu definování výpočetní kapacity a výpočtu nákladů pro výpočetní vrstvu bez serveru naleznete v [tématu SQL Database serverless](sql-database-serverless.md).

## <a name="storage-costs"></a>Cena za uložení

Různé typy úložiště se účtují odlišně. Pro ukládání dat se vám účtuje zřízené úložiště na základě maximální velikosti databáze nebo fondu, kterou vyberete. Náklady se nezmění, pokud toto maximum nesnížíte nebo nezvýšíte. Úložiště záloh je přidruženo k automatickému zálohování vaší instance a je přidělováno dynamicky. Zvýšení doby uchování záloh zvyšuje úložiště záloh, které spotřebovává vaše instance.

Ve výchozím nastavení se 7 dní automatického zálohování databází zkopíruje do účtu úložiště objektů BLOB pro přístup ke čtení (RA-GRS). Toto úložiště se používá týdenní úplné zálohy, denní rozdílové zálohy a zálohy transakční protokol, které jsou zkopírovány každých 5 minut. Velikost protokolů transakcí závisí na rychlosti změny databáze. Minimální velikost úložiště rovnající se 100 procent velikosti databáze je poskytována bez dalších poplatků. Další spotřeba úložiště záloh se účtuje v GB za měsíc.

Další informace o cenách úložiště najdete na stránce [s cenami.](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

Virtuální jádro (vCore) představuje logický procesor a nabízí možnost volby mezi generacemi hardwaru a fyzickými vlastnostmi hardwaru (například počet jader, paměť a velikost úložiště). Nákupní model založený na virtuálních jádrech poskytuje flexibilitu, kontrolu, transparentnost spotřeby jednotlivých prostředků a jednoduchý způsob, jak převést požadavky na místní úlohy do cloudu. Tento model umožňuje zvolit výpočetní prostředky, paměť a prostředky úložiště na základě vašich potřeb úlohy.

V nákupním modelu založeném na virtuálních jádrech si můžete vybrat mezi úrovněmi služeb [obecné účely](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) a důležité [pro podnikání](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) pro jednotlivé databáze , [elastické](sql-database-elastic-pool.md) [fondy](sql-database-single-database-scale.md)a [spravované instance](sql-database-managed-instance.md). Pro jednotlivé databáze můžete také zvolit [úroveň služby Hyperscale](sql-database-service-tier-hyperscale.md).

Nákupní model založený na virtuálních jádrech umožňuje nezávisle zvolit výpočetní prostředky a prostředky úložiště, přizpůsobit místní výkon a optimalizovat cenu. V nákupním modelu založeném na virtuálních jádrech platíte za:

- Výpočetní prostředky (úroveň služby + počet virtuálních jader a množství paměti + generování hardwaru).
- Typ a množství dat a úložiště protokolu.
- Záložní úložiště (RA-GRS).

> [!IMPORTANT]
> Výpočetní prostředky, vstupně-výkon a úložiště dat a protokolů se účtují podle databáze nebo elastického fondu. Úložiště záloh se účtuje za každou databázi. Další informace o poplatcích za spravovanou instanci naleznete v [tématu spravované instance](sql-database-managed-instance.md).
> **Omezení oblasti:** Aktuální seznam podporovaných oblastí naleznete v tématu [produkty dostupné podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Pokud chcete vytvořit spravovanou instanci v oblasti, která momentálně není podporovaná, [odešlete žádost o podporu prostřednictvím portálu Azure](quota-increase-request.md).

Pokud vaše jediná databáze nebo elastický fond spotřebovává více než 300 DTU, převod na nákupní model založený na virtuálních jádrech může snížit vaše náklady. Můžete převést pomocí rozhraní API volby nebo pomocí portálu Azure, bez prostojů. Konverze však není vyžadována a není provedena automaticky. Pokud nákupní model založený na DTU splňuje vaše požadavky na výkon a obchodní požadavky, měli byste jej nadále používat.

Chcete-li převést z nákupního modelu založeného na DTU na nákupní model založený na virtuálních jádrech, vyberte velikost výpočetních prostředků pomocí následujících pravidel:

- Každých 100 DTU ve standardní vrstvě vyžaduje alespoň 1 virtuální jádro ve vrstvě služby pro obecné účely.
- Každých 125 Jednotek DTU na úrovni premium vyžaduje alespoň 1 virtuální jádro ve vrstvě služby Business Critical.

> [!NOTE]
> Pokyny pro velikost i pro virtuální jádra jsou přibližné a jsou k dispozici jako pomoc při počátečním odhadu cíle cílové databázové služby. Optimální konfigurace cílové databáze závisí na zatížení. 
> 
> Dosažení optimálního poměru cena/výkon může vyžadovat využití flexibility modelu virtuálních jader k úpravě počtu virtuálních jader, [generování hardwaru](sql-database-service-tiers-vcore.md#hardware-generations), [úrovně služby](sql-database-service-tiers-vcore.md#service-tiers) a [výpočetní](sql-database-service-tiers-vcore.md#compute-tiers) úrovně a ladění dalších parametrů konfigurace databáze, jako je [maximální stupeň paralelismu](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).

## <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

Jednotka transakce databáze (DTU) představuje blended míra procesoru, paměti, čtení a zápisy. Nákupní model založený na DTU nabízí sadu předkonfigurovaných balíčků výpočetních prostředků a zahrnuté úložiště pro různé úrovně výkonu aplikací. Pokud dáváte přednost jednoduchosti předkonfigurovaného balíčku a pevných plateb každý měsíc, model založený na DTU může být vhodnější pro vaše potřeby.

V nákupním modelu založeném na DTU si můžete vybrat mezi základními, standardními a prémiovými úrovněmi služeb pro [jednotlivé databáze](sql-database-single-database-scale.md) i [elastické fondy](sql-database-elastic-pool.md). Nákupní model založený na DTU není k dispozici pro [spravované instance](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Jednotky databázových transakcí (DTU)

Pro jednu databázi s určitou velikostí výpočetních prostředků v rámci [úrovně služby](sql-database-single-database-scale.md)microsoft garantuje určitou úroveň prostředků pro tuto databázi (nezávisle na jakékoli jiné databázi v cloudu Azure). Tato záruka poskytuje předvídatelnou úroveň výkonu. Množství prostředků přidělených pro databázi se vypočítá jako počet DTU a je svázaný míra výpočetních, úložných a vstupně-v prostředků.

Poměr mezi těmito prostředky je původně určen [zatížením srovnávacího testu online zpracování transakcí (OLTP),](sql-database-benchmark-overview.md) které je navrženo tak, aby bylo typické pro úlohy OLTP v reálném světě. Pokud vaše úloha překročí množství některého z těchto prostředků, vaše propustnost je omezená, což má za následek pomalejší výkon a časové limity.

Prostředky používané vaší úlohou nemají vliv na prostředky, které jsou k dispozici pro jiné databáze SQL v cloudu Azure. Podobně prostředky používané jinými úlohami nemají vliv na prostředky, které jsou k dispozici pro databázi SQL.

![ohraničovací rámeček](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU jsou nejužitečnější pro pochopení relativní prostředky, které jsou přiděleny pro databáze Azure SQL na různé výpočetní velikosti a úrovně služeb. Například:

- Zdvojnásobení DTU zvýšením výpočetní velikost databáze se rovná zdvojnásobení sady prostředků, které jsou k dispozici pro tuto databázi.
- Databáze P11 úrovně premium úrovně služby s 1750 DTU poskytuje 350x více výpočetního výkonu DTU než základní databáze úrovně služby s 5 DTU.  

Chcete-li získat hlubší přehled o spotřebě prostředků (DTU) vašeho pracovního vytížení, použijte [přehledy o výkonu dotazů](sql-database-query-performance.md) k:

- Identifikujte nejvyšší dotazy podle počtu procesoru/doby trvání/spuštění, které lze potenciálně vyladit pro zlepšení výkonu. Například vstupně-va-intenzivní dotaz může těžit z [technik optimalizace v paměti,](sql-database-in-memory.md) aby bylo možné lépe využít dostupné paměti na určité úrovni služby a výpočetní velikost.
- Přejděte k podrobnostem dotazu a zobrazte jeho text a jeho historii využití prostředků.
- Přístup k doporučením pro optimalizaci výkonu, která zobrazují akce provedené [programem SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednotky transakcí elastické databáze (eDTU)

Pro databáze SQL, které jsou vždy k dispozici, spíše než poskytnout vyhrazenou sadu prostředků (DTU), které nemusí být vždy potřeba, můžete umístit tyto databáze do [elastického fondu](sql-database-elastic-pool.md). Databáze v elastickém fondu jsou na jednom serveru Azure SQL Database a sdílejí fond prostředků.

Sdílené prostředky v elastickém fondu se měří jednotkami transakce elastické databáze (eDTU). Elastické fondy poskytují jednoduché a nákladově efektivní řešení pro správu cílů výkonu pro více databází, které mají velmi různé a nepředvídatelné vzorce využití. Elastický fond zaručuje, že všechny prostředky nelze spotřebovat jednu databázi ve fondu, a zároveň zajistit, že každá databáze ve fondu má vždy minimální množství potřebných prostředků k dispozici.

Fondu je přidělen nastavený počet eDTU za nastavenou cenu. V elastickém fondu mohou jednotlivé databáze automaticky škálovat v rámci nakonfigurovaných hranic. Databáze pod těžší zatížení bude spotřebovávat více eDTU k uspokojení poptávky. Databáze pod lehčí zatížení bude spotřebovávat méně eDTU. Databáze bez zatížení nebudou spotřebovávat žádné elektronické prodeje. Vzhledem k tomu, že prostředky jsou zřízeny pro celý fond, nikoli pro databázi, elastické fondy zjednoduší úlohy správy a poskytují předvídatelný rozpočet pro fond.

Můžete přidat další eDTU do existujícího fondu bez prostojů databáze a bez dopadu na databáze ve fondu. Podobně pokud již nepotřebujete další eDTU, odeberte je z existujícího fondu kdykoli. Databáze můžete také kdykoli přidat nebo odečíst z fondu. Chcete-li rezervovat eDTU pro jiné databáze, omezte počet jednotek eDTU, které může databáze použít při velkém zatížení. Pokud databáze konzistentně nevyužívá prostředky, přesuňte ji z fondu a nakonfigurujte ji jako jednu databázi s předvídatelným množstvím požadovaných prostředků.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Určení počtu dtu, které pracovní vytížení potřebuje

Pokud chcete migrovat existující zatížení virtuálního počítače nebo SQL Server virtuálního počítače do Azure SQL Database, použijte [kalkulačku DTU](https://dtucalculator.azurewebsites.net/) k aproximaci počtu potřebných dtu. Pro existující úlohy Azure SQL Database použijte [přehledy výkonu dotazů](sql-database-query-performance.md) k pochopení spotřeby prostředků databáze (DTU) a získejte hlubší přehledy pro optimalizaci úlohy. Zobrazení dynamické správy [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) (DMV) umožňuje zobrazit spotřebu zdrojů za poslední hodinu. Zobrazení [katalogu sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) zobrazuje spotřebu zdrojů za posledních 14 dní, ale s nižší věrností pětiminutových průměrů.

### <a name="determine-dtu-utilization"></a>Určení využití DTU

Chcete-li určit průměrné procento využití DTU/eDTU vzhledem k limitu DTU/eDTU databáze nebo elastického fondu, použijte následující vzorec:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Vstupní hodnoty pro tento vzorec lze získat z [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)a [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMVs. Jinými slovy, chcete-li určit procento využití DTU/eDTU směrem k limitu DTU/eDTU databáze nebo elastického fondu, vyberte největší procentuální hodnotu z následujícího: `avg_cpu_percent`, `avg_data_io_percent`, a `avg_log_write_percent` v daném okamžiku.

> [!NOTE]
> Limit DTU databáze je určen procesoru, čtení, zápisy a paměť k dispozici pro databázi. Však vzhledem k tomu, že databázový stroj SQL Server obvykle `avg_memory_usage_percent` používá všechny dostupné paměti pro jeho mezipaměti dat ke zlepšení výkonu, hodnota bude obvykle blízko 100 % bez ohledu na aktuální zatížení databáze. Proto i v případě, že paměť nepřímo ovlivnit limit DTU, není použit ve vzorci využití DTU.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Úlohy, které využívají elastický fond prostředků

Fondy jsou vhodné pro databáze s nízkým průměrem využití prostředků a relativně řídce využití špičky. Další informace naleznete [v tématu Kdy byste měli zvážit elastický fond databáze SQL?](sql-database-elastic-pool.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Generace hardwaru v nákupním modelu založeném na DTU

V nákupním modelu založeném na DTU si zákazníci nemohou vybrat generaci hardwaru používanou pro jejich databáze. Zatímco daná databáze obvykle zůstává na konkrétní generování hardwaru po dlouhou dobu (obvykle po dobu několika měsíců), existují určité události, které mohou způsobit databázi přesunout na jinou generaci hardwaru.

Databázi lze například přesunout na jinou generaci hardwaru, pokud je vertikálně naváděna nahoru nebo dolů na jiný cíl služby nebo pokud se aktuální infrastruktura v datovém centru blíží limitům kapacity nebo pokud se aktuálně používaný hardware právě používá. vyřazena z provozu z důvodu jeho konce životnosti.

Pokud je databáze přesunuta na jiný hardware, může se změnit výkon pracovního vytížení. Model DTU zaručuje, že propustnost a doba odezvy [zatížení benchmark U DTU](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) zůstane v podstatě totožný jako databáze přesune na jiné generování hardwaru, tak dlouho, dokud jeho cíl služby (počet DTU) zůstane stejný. 

V celém širokém spektru zákaznických úloh spuštěných v Azure SQL Database však může být dopad použití jiného hardwaru pro stejný účel služby výraznější. Různé úlohy budou mít prospěch z různých hardwarových konfigurací a funkcí. Proto pro jiné úlohy než benchmark DTU je možné zobrazit rozdíly výkonu, pokud databáze přesune z jednoho generování hardwaru do jiného.

Například aplikace, která je citlivá na latenci sítě, může vidět lepší výkon na hardwaru Gen5 vs. Gen4 kvůli použití akcelerované sítě v Gen5, ale aplikace využívající intenzivní čtení IO může vidět lepší výkon na hardwaru Gen4 vs. Gen5 kvůli vyšší poměr paměti na jádro na Gen4.

Zákazníci s úlohami, které jsou citlivé na změny hardwaru, nebo zákazníci, kteří chtějí řídit výběr generování hardwaru pro svou databázi, mohou pomocí modelu [virtuálních jader](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) zvolit své upřednostňované generování hardwaru během vytváření a škálování databáze. V modelu virtuálních jader jsou dokumentovány limity prostředků každého cíle služby na každé generaci hardwaru, a to jak pro [jednotlivé databáze,](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) tak pro [elastické fondy](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools). Další informace o generacích hardwaru v modelu virtuálních jader naleznete v [tématu Hardware Generations](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy (ČASTÉ OTÁZKY)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Musím převést svou aplikaci do ústraní, abych mohl převést z úrovně služeb založených na DTU na úroveň služby založené na virtuálních jádrech?

Ne. Aplikaci nemusíte přepojit do pouzdla. Nové úrovně služeb nabízejí jednoduchou metodu online převodu, která je podobná stávajícímu procesu upgradu databází ze standardu na úroveň prémiové služby a naopak. Tuto konverzi můžete spustit pomocí portálu Azure, PowerShellu, rozhraní API Azure, T-SQL nebo rozhraní REST API. Viz [Správa jednotlivých databází](sql-database-single-database-scale.md) a Správa [elastických fondů](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Je možné převést databázi z úrovně služeb v nákupním modelu založeném na virtuálních jádrech na úroveň služeb v nákupním modelu založeném na DTU?

Ano, databázi můžete snadno převést na libovolný účel podporovaného výkonu pomocí portálu Azure, PowerShellu, rozhraní API Azure, T-SQL nebo rozhraní REST API. Viz [Správa jednotlivých databází](sql-database-single-database-scale.md) a Správa [elastických fondů](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Další kroky

- Další informace o nákupním modelu založeném na virtuálních jádrech naleznete v [nákupním modelu založeném na virtuálních jádrech](sql-database-service-tiers-vcore.md).
- Další informace o nákupním modelu založeném na DTU naleznete v [tématu nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
