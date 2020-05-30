---
title: Nákupní modely
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Přečtěte si informace o nákupních modelech, které jsou k dispozici pro Azure SQL Database a Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: a437cd1b3cb8018229cf6445e23314cf17bf10a2
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219862"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database--sql-managed-instance"></a>Výběr mezi modely nákupu vCore a DTU – Azure SQL Database & spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database & spravované instance SQL vám umožní snadno zakoupit plně spravovaný databázový stroj PaaS (Platform as a Service), který vyhovuje vašim požadavkům na výkon a náklady. V závislosti na modelu nasazení, který jste zvolili pro Azure SQL Database, můžete vybrat model nákupu, který vám bude vyhovovat:

- [Model nákupu založený na Virtual Core (Vcore)](service-tiers-vcore.md) (doporučeno) Tento model nákupu nabízí možnost volby mezi zřízenou výpočetní vrstvou a výpočetní úrovní bez serveru. Po zřízené výpočetní úrovni zvolíte přesné množství výpočetních prostředků, které se pro vaše úlohy vždycky zřídí. Pomocí výpočetní úrovně bez serveru určíte automatické škálování výpočetních prostředků přes konfigurovatelný výpočetní rozsah. Pomocí této výpočetní vrstvy můžete také automaticky pozastavit a obnovit databázi na základě aktivity úloh. VCore Jednotková cena za jednotku času je nižší ve zřízené výpočetní úrovni, než je na výpočetní úrovni bez serveru.
- [Nákupní model založený na jednotce pro databázové transakce (DTU)](service-tiers-dtu.md). Tento model nákupu poskytuje balíčky výpočetních a úložných prostředků vyrovnaných pro běžné úlohy.

Existují dva nákupní modely:

- [nákupní model založený na Vcore](service-tiers-vcore.md) je k dispozici pro [Azure SQL Database](sql-database-paas-overview.md) i pro [spravovanou instanci SQL Azure](../managed-instance/sql-managed-instance-paas-overview.md). [Úroveň služby pro škálování](service-tier-hyperscale.md) na úrovni služeb je dostupná pro jednotlivé databáze SQL, které používají [nákupní model založený na Vcore](service-tiers-vcore.md).
- [Nákupní model založený na DTU](service-tiers-dtu.md) je k dispozici pro [Azure SQL Database](single-database-manage.md).

Následující tabulka a graf se porovnávají a kontrastují s nákupními modely založenými na DTU vCore a na DTU:

|**Model nákupu**|**Popis**|**Nejlepší pro**|
|---|---|---|
|Model na základě DTU|Tento model vychází ze sady prostředků COMPUTE, úložiště a vstupně-výstupních prostředků. Velikosti výpočetních hodnot se vyjadřují v DTU pro izolované databáze a v jednotkách eDTU (elastické databázové transakce) pro elastické fondy. Další informace o DTU a eDTU najdete v tématu [co jsou DTU a eDTU?](purchasing-models.md#dtu-based-purchasing-model).|Nejvhodnější pro zákazníky, kteří chtějí jednoduché, předem nakonfigurované možnosti prostředků.|
|Model na základě virtuálních jader|Tento model vám umožní nezávisle zvolit výpočetní prostředky a prostředky úložiště. Nákupní model založený na vCoreech vám také umožní použít [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server k získání úspory nákladů.|Nejvhodnější pro zákazníky, kteří flexibilita hodnot, řízení a transparentnost.|
||||  

![porovnání cenového modelu](./media/purchasing-models/pricing-model.png)

## <a name="compute-costs"></a>Náklady na výpočetní výkon

### <a name="provisioned-compute-costs"></a>Zřízené výpočetní náklady

Ve zřízené výpočetní úrovni se náklady na výpočetní prostředky odráží v celkové výpočetní kapacitě zřízené pro aplikaci.

Ve vrstvě služby Pro důležité obchodní informace automaticky přidělíme aspoň 3 repliky. Vzhledem k tomu, že se toto dodatečné přidělení výpočetních prostředků projeví, cena v nákupním modelu založeném na vCore je přibližně 2.7 × vyšší v úrovni služby Pro důležité obchodní informace, než je v úrovni služby Pro obecné účely. Podobně vyšší cena úložiště na GB v Pro důležité obchodní informace úrovni služby odráží vyšší limity vstupně-výstupní operace a nižší latenci úložiště SSD.

Náklady na úložiště zálohování jsou stejné jako pro úroveň služby Pro důležité obchodní informace a Pro obecné účely úroveň služby, protože obě úrovně používají pro zálohování standardní úložiště.

### <a name="serverless-compute-costs"></a>Náklady na výpočetní prostředky bez serveru

Popis způsobu definování kapacity a výpočtu nákladů na výpočetní úrovni serveru najdete v tématu [SQL Database bez serveru](serverless-tier-overview.md).

## <a name="storage-costs"></a>Cena za uložení

Různé typy úložiště se účtují jinak. Pro úložiště dat se vám budou účtovat zřízené úložiště založené na maximální velikosti databáze nebo fondu, který vyberete. Náklady se nezmění, pokud je maximální hodnota nesnížíte nebo nezvýšíte. Úložiště zálohování je přidruženo k automatizovaným zálohám vaší instance a dynamicky se přiřazuje. Zvýšení doby uchovávání záloh zvyšuje velikost úložiště zálohování spotřebovaného vaší instancí.

Ve výchozím nastavení se 7 dní automatizovaného zálohování vašich databází kopíruje do účtu úložiště s přístupem pro čtení geograficky redundantního úložiště s přístupem pro čtení (RA-GRS). Toto úložiště používají týdenní úplné zálohování, denní rozdílové zálohy a zálohy transakčních protokolů, které se zkopírují každých 5 minut. Velikost protokolů transakcí závisí na rychlosti změny databáze. Minimální velikost úložiště rovnající se 100 procentům velikosti databáze se poskytuje bez dalších poplatků. Další spotřebou úložiště zálohování se účtují za GB za měsíc.

Další informace o cenách za úložiště najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

Virtual Core (vCore) představuje logický procesor a nabízí možnost výběru mezi generacemi hardwaru a fyzickými charakteristikami hardwaru (například počet jader, paměti a velikost úložiště). Nákupní model založený na vCoreech poskytuje flexibilitu, kontrolu, transparentnost individuální spotřeby prostředků a snadný způsob, jak přeložit požadavky na místní úlohy do cloudu. Tento model umožňuje zvolit prostředky výpočtů, paměti a úložiště na základě potřeb vašich úloh.

V rámci nákupního modelu založeného na vCore si můžete vybrat mezi [pro obecné účely](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) a [pro důležité obchodní informace](high-availability-sla.md#premium-and-business-critical-service-tier-availability) úrovněmi služeb pro SQL Database a SQL Managed instance.  U samostatných databází SQL Azure můžete také zvolit [úroveň služby pro škálování na úrovni služeb](service-tier-hyperscale.md).

Nákupní model založený na vCore vám umožňuje nezávisle vybrat výpočetní prostředky a prostředky úložiště, odpovídat místnímu výkonu a optimalizovat ceny. V rámci nákupního modelu založeného na vCore platíte za:

- Výpočetní prostředky (úroveň služby + počet virtuální jádra a množství paměti a generace hardwaru).
- Typ a množství dat a úložiště protokolu.
- Úložiště zálohování (RA-GRS).

> [!IMPORTANT]
> Výpočetní prostředky, vstupně-výstupní operace a data a úložiště protokolů se účtují na základě databáze nebo elastického fondu. Úložiště zálohování se účtuje podle každé databáze. Další informace o poplatcích za spravované instance SQL najdete v tématu [spravované instance SQL](../managed-instance/sql-managed-instance-paas-overview.md).
> **Omezení oblastí:** Aktuální seznam podporovaných oblastí najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Pokud chcete vytvořit spravovanou instanci SQL v oblasti, která v tuto chvíli není podporovaná, [pošlete žádost o podporu prostřednictvím Azure Portal](quota-increase-request.md).

Pokud vaše Azure SQL Database spotřebovává více než 300 DTU, může převod na model nákupu na základě vCore snížit vaše náklady. Můžete převést pomocí rozhraní API podle vlastního výběru nebo pomocí Azure Portal bez výpadků. Konverze ale není povinná a neprovádí se automaticky. Pokud nákupní model založený na DTU splňuje vaše požadavky na výkon a podnikání, měli byste ho dál používat.

Chcete-li provést převod z nákupního modelu založeného na DTU na nákupní model založený na vCore, vyberte výpočetní velikost pomocí následujících pravidel pro palec:

- Každé 100 DTU na úrovni Standard vyžaduje aspoň 1 vCore v úrovni služby Pro obecné účely.
- Každé 125 DTU na úrovni Premium vyžaduje alespoň 1 vCore v úrovni služby Pro důležité obchodní informace.

> [!NOTE]
> Pokyny ke změně velikosti DTU na vCore jsou přibližné a poskytují se při počátečním odhadu cíle cílové databázové služby. Optimální konfigurace cílové databáze je závislá na úlohách.
>
> Dosažení optimálního poměru cen a výkonu může vyžadovat využití flexibility modelu vCore k úpravě počtu virtuální jádra, [generování hardwaru](service-tiers-vcore.md#hardware-generations), [služby](service-tiers-vcore.md#service-tiers) a [výpočetních](service-tiers-vcore.md#compute-tiers) vrstev a také k ladění dalších parametrů konfigurace databáze, jako je například [Maximální stupeň paralelismu](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).

## <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

Jednotka pro transakce databáze (DTU) představuje míchanou míru procesoru, paměti, čtení a zápisu. Nákupní model založený na DTU nabízí sadu předkonfigurovaných sad výpočetních prostředků a zahrnutého úložiště pro zajištění různých úrovní výkonu aplikace. Pokud dáváte přednost jednoduchosti předkonfigurovaných sad a pevných plateb každý měsíc, model založený na DTU může být vhodnější pro vaše potřeby.

V nákupním modelu založeném na DTU si můžete vybrat mezi úrovněmi služeb Basic, Standard a Premium pro Azure SQL Database. Nákupní model založený na DTU není pro spravovanou instanci SQL Azure k dispozici.

### <a name="database-transaction-units-dtus"></a>Jednotky transakcí databáze (DTU)

Pro izolovanou databázi s určitou výpočetní velikostí v rámci [vrstvy služeb](single-database-scale.md)poskytuje Azure určitou úroveň prostředků pro tuto databázi (nezávisle na jiných databázích v cloudu Azure). Tato záruka poskytuje předvídatelné množství výkonu. Množství prostředků přidělených pro databázi se vypočítává jako počet DTU a jedná se o rozdělené měřítko prostředků výpočtů, úložiště a vstupně-výstupních prostředků.

Poměr mezi těmito prostředky je původně určený [úlohou srovnávacích testů online zpracování transakcí (OLTP)](service-tiers-dtu.md) , která je navržená tak, aby byla typická pro reálné OLTP úlohy. Když vaše úloha překročí množství těchto prostředků, vaše propustnost je omezená a výsledkem je pomalejší výkon a časové limity.

Prostředky používané úlohou nemají vliv na prostředky, které jsou k dispozici pro jiné databáze SQL v cloudu Azure. Podobně prostředky používané jinými úlohami nemají vliv na prostředky, které jsou k dispozici pro váš SQL Database.

![ohraničovací rámeček](./media/purchasing-models/bounding-box.png)

DTU jsou nejužitečnější pro porozumění relativním prostředkům, které jsou přiděleny pro databáze SQL Azure v různých velikostech výpočtů a úrovních služby. Příklad:

- Zdvojnásobuje se DTU zvýšením velikosti databáze, která je rovna zdvojnásobení sady prostředků, které jsou k dispozici pro danou databázi.
- P11 databáze úrovně Premium Service s 1750 DTU poskytuje výpočetní výkon 350x větší než základní databáze úrovně služeb s 5 DTU.  

Pokud chcete získat hlubší přehled o spotřebě prostředků (DTU) pro vaše úlohy, použijte [přehledy výkonu dotazů](query-performance-insight-use.md) na:

- Identifikujte hlavní dotazy podle počtu PROCESORů, doby trvání a počtu spuštění, které je možné optimalizovat pro zlepšení výkonu. Například dotaz náročné na vstupně-výstupní operace může využívat [techniky optimalizace v paměti](../in-memory-oltp-overview.md) , aby bylo možné lépe využívat dostupnou paměť v určité úrovni služby a výpočetní velikost.
- Přechodem k podrobnostem dotazu zobrazíte jeho text a historii využití prostředků.
- Přístup k doporučením pro ladění výkonu, která zobrazují akce prováděné [SQL Database Advisor](database-advisor-implement-performance-recommendations.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednotky transakce elastické databáze (eDTU)

Pro databáze SQL, které jsou vždy k dispozici, místo poskytování vyhrazené sady prostředků (DTU), které nemusí být vždy potřeba, můžete tyto databáze umístit do [elastického fondu](elastic-pool-overview.md). Databáze v elastickém fondu jsou na jednom serveru a sdílejí fond prostředků.

Sdílené prostředky v elastickém fondu se měří podle jednotek elastické databázové transakce (eDTU). Elastické fondy poskytují jednoduché a nákladově výhodné řešení pro správu cílů výkonu pro více databází, které mají široce proměnlivé a nepředvídatelné vzorce používání. Elastický fond zaručuje, že všechny prostředky nemůže využívat jedna databáze ve fondu, a přitom zajišťuje, aby každá databáze ve fondu měla vždy minimální množství nezbytných prostředků k dispozici.

Fondu je udělený nastavený počet eDTU pro stanovenou cenu. V elastickém fondu se můžou jednotlivé databáze škálovat v rámci nakonfigurovaných hranic. Databáze v rámci těžšího zatížení bude spotřebovávat více eDTU, aby splnila požadavky. Databáze v rámci světlejšího zatížení budou spotřebovávat méně eDTU. Databáze bez zatížení nebudou využívat žádné eDTU. Vzhledem k tomu, že prostředky jsou zřízené pro celý fond, a nikoli pro jednotlivé databáze, elastické fondy zjednodušují úlohy správy a poskytují předvídatelný rozpočet pro fond.

Do existujícího fondu můžete přidat další eDTU bez výpadku databáze a bez dopadu na databáze ve fondu. Podobně pokud již nepotřebujete dodatečně eDTU, odeberte je z existujícího fondu kdykoli. Databáze můžete z fondu kdykoli přidávat nebo je z něj odebírat. Pokud chcete vyhradit eDTU pro jiné databáze, omezte počet eDTU, které databáze může použít při velkém zatížení. Pokud databáze konzistentně využívá prostředky, přesuňte ji mimo fond a nakonfigurujte ji jako izolovanou databázi s předvídatelným množstvím požadovaných prostředků.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Určení počtu DTU potřebných pro zatížení

Pokud chcete migrovat existující úlohu místního nebo SQL Server virtuálního počítače na SQL Database, použijte [kalkulačku DTU](https://dtucalculator.azurewebsites.net/) k aproximaci počtu potřebných DTU. Pro existující úlohu SQL Database použijte [přehledy výkonu dotazů](query-performance-insight-use.md) , které vám pomůžou pochopit využití prostředků databáze (DTU) a získat hlubší přehledy pro optimalizaci vašich úloh. Zobrazení dynamické správy [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) (DMV) umožňuje zobrazit spotřebu prostředků za poslední hodinu. V zobrazení katalogu [Sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) se zobrazuje spotřeba prostředků za posledních 14 dní, ale s nižší věrností průměrných průměrných rychlostí 5 minut.

### <a name="determine-dtu-utilization"></a>Určení využití DTU

K určení průměrného procenta využití DTU/eDTU vzhledem k limitu DTU/eDTU databáze nebo elastického fondu použijte následující vzorec:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Vstupní hodnoty pro tento vzorec lze získat z [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [Sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)a [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) zobrazení dynamické správy. Jinými slovy, chcete-li určit procento využití DTU/eDTU do limitu DTU/eDTU databáze nebo elastického fondu, vyberte nejvyšší procento z následujících hodnot: `avg_cpu_percent` , `avg_data_io_percent` a v `avg_log_write_percent` daném časovém okamžiku.

> [!NOTE]
> Omezení DTU databáze je určeno PROCESORem, čtením, zápisy a pamětí, která je k dispozici pro databázi. Vzhledem k tomu, že modul SQL Database obvykle používá veškerou dostupnou paměť pro jeho datovou mezipaměť ke zvýšení výkonu, `avg_memory_usage_percent` hodnota se obvykle blíží 100%, bez ohledu na aktuální zatížení databáze. Proto i když paměť nepřímo ovlivňuje limit DTU, nepoužívá se ve vzorci využití DTU.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Úlohy, které využívají elastický fond prostředků

Fondy jsou vhodné pro databáze s nízkým průměrem využití prostředků a relativně málo časté špičky využití. Další informace najdete v tématu [Kdy byste měli zvážit SQL Database elastický fond?](elastic-pool-overview.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Generace hardwaru v modelu nákupu založeném na DTU

V nákupním modelu založeném na DTU si zákazníci nemůžou zvolit generování hardwaru používaného pro své databáze. Zatímco daná databáze obvykle zůstává na konkrétní generaci hardwaru po dlouhou dobu (obvykle více měsíců), existují určité události, které mohou způsobit přesun databáze do jiné generace hardwaru.

Databázi můžete například přesunout do jiné generace hardwaru, pokud je rozdělená nahoru nebo dolů na jiný cíl služby, nebo pokud se aktuální infrastruktura v datovém centru blíží k omezením kapacity nebo pokud se aktuálně použitý hardware vyřadí z provozu v důsledku konce životnosti.

Pokud je databáze přesunuta na jiný hardware, výkon úlohy se může změnit. Model DTU zaručuje, že propustnost a doba odezvy úlohy [srovnávacího testu DTU](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) zůstanou v podstatě stejné jako databáze se přesune na jinou generaci hardwaru, pokud je její cíl služby (počet DTU) zůstává stejný.

V rámci spektra úloh zákazníků, které běží v Azure SQL Database, je ale možné vyslovit dopad použití jiného hardwaru pro stejný cíl služby. Různé úlohy budou využívat různé konfigurace hardwaru a funkce. Pro jiné úlohy, než je srovnávací test DTU, je proto možné sledovat rozdíly v výkonu, pokud se databáze přesouvá z jedné generace hardwaru do jiné.

Například aplikace, která je citlivá na latenci sítě, může zobrazit lepší výkon na Gen5 hardwaru vs. COMPUTE GEN4 – z důvodu použití akcelerovaných síťových služeb v Gen5, ale aplikace, která používá intenzivní vstupně-výstupní operace, může zobrazit lepší výkon na COMPUTE GEN4 – hardwaru vs. Gen5 kvůli vyššímu poměru paměti na jádro COMPUTE GEN4 –.

Zákazníci s úlohami citlivými na změny hardwaru nebo zákazníky, kteří chtějí řídit výběr generování hardwaru pro svou databázi, můžou pomocí modelu [Vcore](service-tiers-vcore.md) zvolit jejich preferovanou generaci hardwaru během vytváření a škálování databáze. V modelu vCore se pro jednotlivé [databáze](resource-limits-vcore-single-databases.md) a [elastické fondy](resource-limits-vcore-elastic-pools.md)zdokumentují omezení prostředků každého cíle služby při každém generování hardwaru. Další informace o generacích hardwaru v modelu vCore najdete v tématu [hardwarové generace](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Potřebuji aplikaci převést do režimu offline, aby se převedla z úrovně služby založené na DTU na úroveň služby založenou na vCore?

No. Nemusíte přebírat aplikaci offline. Nové úrovně služeb nabízejí jednoduchou metodu online převodu, která je podobná stávajícímu procesu upgradu databází z úrovně Standard na úroveň služby Premium a dalším způsobem. Tento převod můžete spustit pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, T-SQL nebo REST API. Viz [Správa](single-database-scale.md) izolovaných databází a [Správa elastických fondů](elastic-pool-overview.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Můžu převést databázi z vrstvy služeb v nákupním modelu založeném na vCore na úroveň služby v nákupním modelu založeném na DTU?

Ano, databázi můžete snadno převést na jakýkoli podporovaný cíl výkonu pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, T-SQL nebo REST API. Viz [Správa](single-database-scale.md) izolovaných databází a [Správa elastických fondů](elastic-pool-overview.md).

## <a name="next-steps"></a>Další kroky

- Další informace o modelu nakupování založeném na vCore najdete v tématu [nákupní model založený na Vcore](service-tiers-vcore.md).
- Další informace o modelu nákupu na základě DTU naleznete v tématu [nákupní model založený na DTU](service-tiers-dtu.md).
