---
title: Nákupní modely
description: Přečtěte si informace o nákupních modelech, které jsou k dispozici pro Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: be223245c634b8e38dc5e4c89df4c265c22c0b57
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821263"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Volba mezi vCore a nákupními modely DTU

Azure SQL Database vám umožní snadno zakoupit plně spravovaný databázový stroj PaaS (Platform as a Service), který vyhovuje vašim požadavkům na výkon a náklady. V závislosti na modelu nasazení, který jste zvolili pro Azure SQL Database, můžete vybrat model nákupu, který vám bude vyhovovat:

- [Model nákupu založený na Virtual Core (Vcore)](sql-database-service-tiers-vcore.md) (doporučeno) Tento model nákupu nabízí možnost volby mezi zřízenou výpočetní vrstvou a výpočetní úrovní bez serveru. Po zřízené výpočetní úrovni zvolíte přesné množství výpočetních prostředků, které se pro vaše úlohy vždycky zřídí. Pomocí výpočetní úrovně bez serveru určíte automatické škálování výpočetních prostředků přes konfigurovatelný výpočetní rozsah. Pomocí této výpočetní vrstvy můžete také automaticky pozastavit a obnovit databázi na základě aktivity úloh. VCore Jednotková cena za jednotku času je nižší ve zřízené výpočetní úrovni, než je na výpočetní úrovni bez serveru.
- [Nákupní model založený na jednotce pro databázové transakce (DTU)](sql-database-service-tiers-dtu.md). Tento model nákupu poskytuje balíčky výpočetních a úložných prostředků vyrovnaných pro běžné úlohy.

Pro různé modely nasazení Azure SQL Database jsou k dispozici různé nákupní modely:

- Možnosti nasazení [jedna databáze](sql-database-single-databases-manage.md) a [elastický fond](sql-database-elastic-pool.md) v [Azure SQL Database](sql-database-technical-overview.md) nabízejí jak [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) , tak i [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md).
- Možnost nasazení [Managed instance](sql-database-managed-instance.md) v Azure SQL Database nabízí jenom [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md).
- [Úroveň služby pro škálování](sql-database-service-tier-hyperscale.md) na úrovni služeb je k dispozici pro izolované databáze, které používají [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md).

Následující tabulka a graf se porovnávají a kontrastují s nákupními modely založenými na DTU vCore a na DTU:

|**Model nákupu**|**Popis**|**Nejlepší pro**|
|---|---|---|
|Model na základě DTU|Tento model vychází ze sady prostředků COMPUTE, úložiště a vstupně-výstupních prostředků. Velikosti výpočetních hodnot se vyjadřují v DTU pro izolované databáze a v jednotkách eDTU (elastické databázové transakce) pro elastické fondy. Další informace o DTU a eDTU najdete v tématu [co jsou DTU a eDTU?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Nejvhodnější pro zákazníky, kteří chtějí jednoduché, předem nakonfigurované možnosti prostředků.|
|Model na základě virtuálních jader|Tento model vám umožní nezávisle zvolit výpočetní prostředky a prostředky úložiště. Nákupní model založený na vCoreech vám také umožní použít [Zvýhodněné hybridní využití Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) k získání úspory nákladů.|Nejvhodnější pro zákazníky, kteří flexibilita hodnot, řízení a transparentnost.|
||||  

![porovnání cenového modelu](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Náklady na výpočetní výkon

### <a name="provisioned-compute-costs"></a>Zřízené výpočetní náklady

Ve zřízené výpočetní úrovni se náklady na výpočetní prostředky odráží v celkové výpočetní kapacitě zřízené pro aplikaci.

Na úrovni služby důležité pro podnikání automaticky přidělíme aspoň 3 repliky. Vzhledem k tomu, že se toto dodatečné přidělení výpočetních prostředků projeví, cena v nákupním modelu založeném na vCore je přibližně 2.7 × vyšší v úrovni služeb pro důležité obchodní informace, než je v úrovni služby pro obecné účely. Podobně vyšší cena úložiště za GB v úrovni služby důležité pro podnikání odráží vysoké vstupně-výstupní operace a nízkou latenci úložiště SSD.

Náklady na úložiště zálohování jsou stejné jako u důležitých podnikových služeb a na úrovni služby pro obecné účely, protože obě úrovně používají úložiště Standard Storage úrovně Standard.

### <a name="serverless-compute-costs"></a>Náklady na výpočetní prostředky bez serveru

Popis způsobu definování kapacity a výpočtu nákladů na výpočetní úrovni serveru najdete v tématu [SQL Database bez serveru](sql-database-serverless.md).

## <a name="storage-costs"></a>Cena za uložení

Různé typy úložiště se účtují jinak. Pro úložiště dat se vám budou účtovat zřízené úložiště založené na maximální velikosti databáze nebo fondu, který vyberete. Náklady se nezmění, pokud je maximální hodnota nesnížíte nebo nezvýšíte. Úložiště zálohování je přidruženo k automatizovaným zálohám vaší instance a dynamicky se přiřazuje. Zvýšení doby uchovávání záloh zvyšuje velikost úložiště zálohování spotřebovaného vaší instancí.

Ve výchozím nastavení se 7 dní automatizovaného zálohování vašich databází kopíruje do účtu úložiště s přístupem pro čtení geograficky redundantního úložiště s přístupem pro čtení (RA-GRS). Toto úložiště používají týdenní úplné zálohování, denní rozdílové zálohy a zálohy transakčních protokolů, které se zkopírují každých 5 minut. Velikost protokolů transakcí závisí na rychlosti změny databáze. Minimální velikost úložiště rovnající se 100 procentům velikosti databáze se poskytuje bez dalších poplatků. Další spotřebou úložiště zálohování se účtují za GB za měsíc.

Další informace o cenách za úložiště najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

Virtual Core (vCore) představuje logický procesor a nabízí možnost výběru mezi generacemi hardwaru a fyzickými charakteristikami hardwaru (například počet jader, paměti a velikost úložiště). Nákupní model založený na vCoreech poskytuje flexibilitu, kontrolu, transparentnost individuální spotřeby prostředků a snadný způsob, jak přeložit požadavky na místní úlohy do cloudu. Tento model umožňuje zvolit prostředky výpočtů, paměti a úložiště na základě potřeb vašich úloh.

V rámci nákupního modelu založeného na vCore si můžete vybrat mezi úrovněmi služeb pro [jednotlivé databáze](sql-database-single-database-scale.md), [elastické fondy](sql-database-elastic-pool.md)a [spravované instance](sql-database-managed-instance.md)z hlediska [obecných účelů](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) a [důležitých podnikových](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) služeb. Pro izolované databáze můžete také zvolit [úroveň služby pro škálování na úrovni služeb](sql-database-service-tier-hyperscale.md).

Nákupní model založený na vCore vám umožňuje nezávisle vybrat výpočetní prostředky a prostředky úložiště, odpovídat místnímu výkonu a optimalizovat ceny. V rámci nákupního modelu založeného na vCore platíte za:

- Výpočetní prostředky (úroveň služby + počet virtuální jádra a množství paměti a generace hardwaru).
- Typ a množství dat a úložiště protokolu.
- Úložiště zálohování (RA-GRS).

> [!IMPORTANT]
> Výpočetní prostředky, vstupně-výstupní operace a data a úložiště protokolů se účtují na základě databáze nebo elastického fondu. Úložiště zálohování se účtuje podle každé databáze. Další informace o poplatcích za spravované instance najdete v tématu [spravované instance](sql-database-managed-instance.md).
> **Omezení oblastí:** Aktuální seznam podporovaných oblastí najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Pokud chcete vytvořit spravovanou instanci v oblasti, která v tuto chvíli není podporovaná, [pošlete žádost o podporu prostřednictvím Azure Portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Pokud vaše izolovaná databáze nebo elastický fond spotřebovává více než 300 DTU, může převod na model nákupu na základě vCore snížit vaše náklady. Můžete převést pomocí rozhraní API podle vlastního výběru nebo pomocí Azure Portal bez výpadků. Konverze ale není povinná a neprovádí se automaticky. Pokud nákupní model založený na DTU splňuje vaše požadavky na výkon a podnikání, měli byste ho dál používat.

Chcete-li provést převod z nákupního modelu založeného na DTU na nákupní model založený na vCore, vyberte výpočetní velikost pomocí následujících pravidel pro palec:

- Každé 100 DTU na úrovni Standard vyžaduje aspoň 1 vCore na úrovni služby pro obecné účely.
- Každé 125 DTU na úrovni Premium vyžaduje aspoň 1 vCore na úrovni služby důležité pro podnikání.

## <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

Jednotka pro transakce databáze (DTU) představuje míchanou míru procesoru, paměti, čtení a zápisu. Nákupní model založený na DTU nabízí sadu předkonfigurovaných sad výpočetních prostředků a zahrnutého úložiště pro zajištění různých úrovní výkonu aplikace. Pokud dáváte přednost jednoduchosti předkonfigurovaných sad a pevných plateb každý měsíc, model založený na DTU může být vhodnější pro vaše potřeby.

V modelu nákupu založeném na DTU si můžete vybrat mezi úrovněmi služeb Basic, Standard a Premium pro [jednotlivé databáze](sql-database-single-database-scale.md) i [elastické fondy](sql-database-elastic-pool.md). Nákupní model založený na DTU není pro [spravované instance](sql-database-managed-instance.md)k dispozici.

### <a name="database-transaction-units-dtus"></a>Jednotky transakcí databáze (DTU)

Pro izolovanou databázi s určitou výpočetní velikostí v rámci [vrstvy služeb](sql-database-single-database-scale.md)společnost Microsoft garantuje určitou úroveň prostředků pro tuto databázi (nezávisle na jiných databázích v cloudu Azure). Tato záruka poskytuje předvídatelné množství výkonu. Množství prostředků přidělených pro databázi se vypočítává jako počet DTU a jedná se o rozdělené měřítko prostředků výpočtů, úložiště a vstupně-výstupních prostředků.

Poměr mezi těmito prostředky je původně určený [úlohou srovnávacích testů online zpracování transakcí (OLTP)](sql-database-benchmark-overview.md) , která je navržená tak, aby byla typická pro reálné OLTP úlohy. Když vaše úloha překročí množství těchto prostředků, vaše propustnost je omezená a výsledkem je pomalejší výkon a časové limity.

Prostředky používané úlohou nemají vliv na prostředky, které jsou k dispozici pro jiné databáze SQL v cloudu Azure. Podobně prostředky používané jinými úlohami nemají vliv na prostředky, které jsou k dispozici pro vaši databázi SQL.

![ohraničovací rámeček](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU jsou nejužitečnější pro porozumění relativním prostředkům, které jsou přiděleny pro databáze SQL Azure v různých velikostech výpočtů a úrovních služby. Příklad:

- Zdvojnásobuje se DTU zvýšením velikosti databáze, která je rovna zdvojnásobení sady prostředků, které jsou k dispozici pro danou databázi.
- P11 databáze úrovně Premium Service s 1750 DTU poskytuje výpočetní výkon 350x větší než základní databáze úrovně služeb s 5 DTU.  

Pokud chcete získat hlubší přehled o spotřebě prostředků (DTU) pro vaše úlohy, použijte [přehledy výkonu dotazů](sql-database-query-performance.md) na:

- Identifikujte hlavní dotazy podle počtu PROCESORů, doby trvání a počtu spuštění, které je možné optimalizovat pro zlepšení výkonu. Například dotaz náročné na vstupně-výstupní operace může využívat [techniky optimalizace v paměti](sql-database-in-memory.md) , aby bylo možné lépe využívat dostupnou paměť v určité úrovni služby a výpočetní velikost.
- Přechodem k podrobnostem dotazu zobrazíte jeho text a historii využití prostředků.
- Přístup k doporučením pro ladění výkonu, která zobrazují akce prováděné [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednotky transakce elastické databáze (eDTU)

Pro databáze SQL, které jsou vždy k dispozici, místo poskytování vyhrazené sady prostředků (DTU), které nemusí být vždy potřeba, můžete tyto databáze umístit do [elastického fondu](sql-database-elastic-pool.md). Databáze v elastickém fondu jsou na jednom Azure SQL Databaseovém serveru a sdílejí fond prostředků.

Sdílené prostředky v elastickém fondu se měří podle jednotek elastické databázové transakce (eDTU). Elastické fondy poskytují jednoduché a nákladově výhodné řešení pro správu cílů výkonu pro více databází, které mají široce proměnlivé a nepředvídatelné vzorce používání. Elastický fond zaručuje, že všechny prostředky nemůže využívat jedna databáze ve fondu, a přitom zajišťuje, aby každá databáze ve fondu měla vždy minimální množství nezbytných prostředků k dispozici.

Fondu je udělený nastavený počet eDTU pro stanovenou cenu. V elastickém fondu se můžou jednotlivé databáze škálovat v rámci nakonfigurovaných hranic. Databáze v rámci těžšího zatížení bude spotřebovávat více eDTU, aby splnila požadavky. Databáze v rámci světlejšího zatížení budou spotřebovávat méně eDTU. Databáze bez zatížení nebudou využívat žádné eDTU. Vzhledem k tomu, že prostředky jsou zřízené pro celý fond, a nikoli pro jednotlivé databáze, elastické fondy zjednodušují úlohy správy a poskytují předvídatelný rozpočet pro fond.

Do existujícího fondu můžete přidat další eDTU bez výpadku databáze a bez dopadu na databáze ve fondu. Podobně pokud již nepotřebujete dodatečně eDTU, odeberte je z existujícího fondu kdykoli. Databáze můžete z fondu kdykoli přidávat nebo je z něj odebírat. Pokud chcete vyhradit eDTU pro jiné databáze, omezte počet eDTU, které databáze může použít při velkém zatížení. Pokud databáze konzistentně nepoužívá prostředky, přesuňte ji mimo fond a nakonfigurujte ji jako izolovanou databázi s předvídatelným množstvím požadovaných prostředků.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Určení počtu DTU potřebných pro zatížení

Pokud chcete migrovat existující úlohu místního nebo SQL Server virtuálního počítače na Azure SQL Database, použijte [kalkulačku DTU](https://dtucalculator.azurewebsites.net/) k aproximaci počtu potřebných DTU. Pro existující úlohu Azure SQL Database použijte [přehledy výkonu dotazů](sql-database-query-performance.md) , které vám pomůžou pochopit využití prostředků databáze (DTU) a získat hlubší přehledy pro optimalizaci vašich úloh. Zobrazení dynamické správy (DMV) [resource_stats sys. dm_db_](https://msdn.microsoft.com/library/dn800981.aspx) umožňuje zobrazit spotřebu prostředků za poslední hodinu. V zobrazení katalogu [Sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) se zobrazuje spotřeba prostředků za posledních 14 dní, ale s nižší věrností průměrných průměrných rychlostí 5 minut.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Úlohy, které využívají elastický fond prostředků

Fondy jsou vhodné pro databáze s nízkým průměrem využití prostředků a relativně málo časté špičky využití. SQL Database automaticky vyhodnocuje historické využití prostředků databáze na stávajícím serveru SQL Database a doporučuje příslušnou konfiguraci fondu v Azure Portal. Další informace najdete v tématu [Kdy byste měli zvážit SQL Database elastický fond?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Potřebuji aplikaci převést do režimu offline, aby se převedla z úrovně služby založené na DTU na úroveň služby založenou na vCore?

Ne. Nemusíte přebírat aplikaci offline. Nové úrovně služeb nabízejí jednoduchou metodu online převodu, která je podobná stávajícímu procesu upgradu databází z úrovně Standard na úroveň služby Premium a dalším způsobem. Tento převod můžete spustit pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, T-SQL nebo REST API. Viz [Správa](sql-database-single-database-scale.md) izolovaných databází a [Správa elastických fondů](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Můžu převést databázi z vrstvy služeb v nákupním modelu založeném na vCore na úroveň služby v nákupním modelu založeném na DTU?

Ano, databázi můžete snadno převést na jakýkoli podporovaný cíl výkonu pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure CLI, T-SQL nebo REST API. Viz [Správa](sql-database-single-database-scale.md) izolovaných databází a [Správa elastických fondů](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Další kroky

- Další informace o modelu nakupování založeném na vCore najdete v tématu [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md).
- Další informace o modelu nákupu na základě DTU naleznete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
