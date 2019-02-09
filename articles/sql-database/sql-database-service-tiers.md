---
title: Azure SQL Database modely nákupu | Dokumentace Microsoftu
description: Další informace o nákupní model modely, které jsou k dispozici databáze ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 3e4e9d9fb3b7e9a66ec3522e046bdca1ecad98c9
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965082"
---
# <a name="azure-sql-database-purchasing-models"></a>Azure SQL Database zakoupení modelů

Azure SQL Database umožňuje jednoduše koupit plně spravovanou PaaS databázový stroj, který nejlépe vyhovuje vašim potřebám výkonu a nákladů. V závislosti na modelu nasazení Azure SQL Database můžete vybrat nákupní model, který nejlépe vyhovuje vašim potřebám:

- [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) (doporučeno), která umožňuje vybrat přesné množství kapacity úložiště a výpočetní potřebné pro vaši úlohu.
- [Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) kde můžete vybrat dodávat výpočetní výkon a úložiště balíčků s vyrovnáváním pro běžné úlohy.

V modelech nasazení Azure SQL Database k dispozici jsou různé modely nákupu:

- [Izolované databáze](sql-database-single-databases-manage.md) a [elastického fondu](sql-database-elastic-pool.md) možností nasazení v [Azure SQL Database](sql-database-technical-overview.md) nabízí i [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).
- [Spravovanou instanci](sql-database-managed-instance.md) nabízí možnost nasazení ve službě Azure SQL Database [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> [Hyperškálovatelného úroveň služby (preview)](sql-database-service-tier-hyperscale.md) je ve verzi public preview pouze pro izolované databáze pomocí virtuálních jader, model nákupu.

Následující tabulku a graf porovnání a kontrast tyto dva modely nákupu.

|**Nákupní model**|**Popis**|**Nejlepší pro**|
|---|---|---|
|Model na základě DTU|Tento model je založen na připojené míře výpočetní prostředky, úložiště a vstupně-VÝSTUPNÍCH prostředků. Výpočetní velikosti se vyjadřují v jednotky transakcí databáze (Dtu) pro izolované databáze a elastické databáze jednotky transakce (Edtu) pro elastické fondy. Další informace o jednotkách Dtu a Edtu najdete v tématu [co jsou jednotky Dtu a Edtu?](sql-database-service-tiers.md#dtu-based-purchasing-model).|Nejvhodnější pro zákazníky, kteří chtějí jednoduché, předem nakonfigurované možnosti prostředku.|
|Model na základě virtuálních jader|Tento model umožňuje zvolit nezávisle na sobě výpočetní a úložnou kapacitu. Nákupní model založený na virtuálních jádrech také umožňuje používat [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) získat úspory nákladů.|Nejvhodnější pro zákazníky, kteří hodnota flexibilitu, řízení a transparentnost.|
||||  

![cenový model](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Náklady na výpočetní výkon

Náklady na výpočetní odráží celkové výpočetní kapacitu, pro kterého je zřízené pro aplikaci. V obchodní vrstvě důležité služby můžeme automaticky přidělit nejméně 3 repliky. Tak, aby odrážela tuto další přidělování výpočetních prostředků, cena v nákupní model založený na virtuálních jádrech je přibližně 2.7 x vyšší v obchodní vrstvě kritické služby než v rámci úrovně služeb pro obecné účely. Ze stejného důvodu vyšší úložiště cena za GB obchodní vrstvě kritické služby odráží vysoké vstupně-výstupní operace a úložiště SSD s nízkou latencí. Ve stejnou dobu náklady na úložiště pro zálohování není liší tyto dvě úrovně vzhledem k tomu v obou případech používáme třídu úložiště úrovně standard.

## <a name="storage-costs"></a>Cena za uložení

Různé typy úložiště se účtují různě. Pro ukládání dat bude vám účtována zřízeného úložiště na základě maximální velikost databáze nebo fondu, které vyberete. Náklady nemění, není-li snížit nebo zvýšit maximální. Úložiště zálohování je spojen s automatickým zálohám vaší instance a dynamicky přidělit. Prodloužení doby uchovávání záloh zvyšuje požadavky na úložiště zálohování vaší instance. Za úložiště zálohování do 100 % celkového zajišťovaného úložiště serveru se neplatí žádné dodatečné poplatky. Využití úložiště zálohování je účtovat GB za měsíc. Když například máte databázové úložiště velikosti 100 GB, získáte 100 GB úložiště zálohování zdarma. Ale pokud je záloha mít objem 110 GB, zaplatíte za dalších 10 GB.

Pro úložiště záloh u jediné databáze bude se vám účtovat na základě průběžné pro úložiště, která byla přidělena k zálohování databáze minus velikosti databáze. Pro úložiště záloh elastického fondu bude se vám účtovat na základě průběžné pro úložiště, která byla přidělena k zálohování databáze všech databází ve fondu minus maximální velikost dat elastického fondu. Libovolný nárůst velikosti databáze nebo elastického fondu nebo zvýšení rychlost transakcí vyžaduje další úložiště a zvyšuje se tím vaše faktura za úložiště zálohování.  Pokud zvýšíte maximální velikost dat, je toto nové množství odečte od velikost úložiště pro zálohování se fakturuje.

## <a name="vcore-based-purchasing-model"></a>Model nákupu na základě virtuálních jader

Virtuální jádro reprezentuje logický procesor nabízený s možností volby mezi generacemi hardwaru a fyzické charakteristiky hardwaru (například počet jader, paměti a velikost úložiště). Nákupní model založený na virtuálních jádrech poskytuje flexibilitu, kontrolu, transparentnost spotřeby jednotlivých prostředků a jednoduchý způsob převodu požadavků místních úlohy do cloudu. Tento model umožňuje zvolit výpočetních, paměťových a úložiště na základě jejich potřebám provádění úloh. V nákupní model založený na virtuálních jádrech můžete vybrat mezi [Obecné](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) a [pro důležité obchodní informace](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) úrovně u služeb [izolované databáze](sql-database-single-database-scale.md), [ elastické fondy](sql-database-elastic-pool.md), a [spravované instance](sql-database-managed-instance.md). Pro izolované databáze, můžete také [hyperškálovatelného úroveň služby (preview)](sql-database-service-tier-hyperscale.md).

Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě vybrat úložnou a výpočetní prostředky, odpovídají zajištění místního výkonu a optimalizovat cena. V nákupní model založený na virtuálních jádrech zákazníci platí za:

- COMPUTE (úrovni služby a počet virtuálních jader a množství paměti a generaci)
- Typ a velikost úložiště dat a protokolů
- Záložní úložiště (pro čtení RA-GRS)

> [!IMPORTANT]
> Výpočetní prostředky, IOs, data a úložiště protokolů se bude fakturovat za databázi nebo elastický fond. Úložiště záloh se účtuje za každou databázi. Další informace o poplatcích spravovanou instanci, najdete v části [spravované instance](sql-database-managed-instance.md).
> **Omezení oblasti:** Aktuální seznam podporovaných oblastí najdete v tématu [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Pokud chcete v oblasti, která je v tuto chvíli nepodporuje vytvoření Managed Instance, můžete si [odeslat žádost o podporu prostřednictvím webu Azure portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).
.

Pokud se elastický fond nebo izolovanou databázi spotřebovává více než 300 Dtu, může převod nákupní model založený na virtuálních jádrech sníží vaše náklady. Pokud se rozhodnete převést, můžete převést pomocí rozhraní API podle výběru nebo na webu Azure portal, bez výpadků. Převod však není povinný a neprovede automaticky. Nákupní model založený na DTU splňuje výkonu a obchodních požadavcích, měli byste pokračovat, jeho použití. Pokud se rozhodnete převést nákupní model založený na DTU na nákupní model založený na virtuálních jádrech, vyberte velikost výpočetního pomocí následující hrubé odhady: 

- Každý 100 DTU ve standardní úrovni vyžaduje alespoň 1 virtuální jádro v úrovni General Purpose
- Každý 125 DTU na úrovni Premium vyžaduje alespoň 1 virtuální jádro v úrovni pro důležité obchodní informace

## <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

Jednotky transakcí databáze (DTU) představuje kombinaci měření procesoru, paměti, čte a zapisuje. Nákupní model založený na DTU nabízí sadu předem nakonfigurované sady výpočetních prostředků a zahrnuté úložiště na jednotce různé úrovně výkonu aplikace. Zákazníci, kteří dávají přednost jednoduchosti, kterou nabízejí předem nakonfigurované sady prostředků a pevných plateb každý měsíc, může se stát model založený na DTU více odpovídají jejich potřebám. V nákupní model založený na DTU Zákazníci si mohou vybrat mezi **základní**, **standardní**, a **premium** úrovně služeb pro obě [izolované databáze](sql-database-single-database-scale.md) a [elastické fondy](sql-database-elastic-pool.md). Tento model nákupu není k dispozici v [spravované instance](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Jednotky transakcí databáze (Dtu)

Pro jednu databázi na daném vypočítat velikost v rámci [úroveň služby](sql-database-single-database-scale.md), Microsoft zaručuje určité úrovně prostředků pro tuto databázi (nezávisle na jakékoli jiné databáze v cloudu Azure), poskytuje předvídatelný úroveň výkon. Objem prostředků se počítá jako počet jednotek databázové transakce nebo počet jednotek Dtu a je jako součást balíčku míra výpočty, úložiště a vstupně-VÝSTUPNÍCH prostředků. Poměr mezi těmito prostředky byl původně určené [úlohou srovnávacího testu OLTP](sql-database-benchmark-overview.md), navržená tak, aby jako typická pro úlohy OLTP v reálném světě. Pokud vaše úloha překračuje množství některý z těchto prostředků, propustnost je omezené – následek pomalejší výkon a časové limity. Prostředky využívané třídou úlohy nemají vliv na prostředky dostupné pro jiné databáze SQL v cloudu Azure a prostředky využívané třídou jiné úlohy nemají vliv na prostředky dostupné pro vaši službu SQL database.

![ohraničujícího rámečku](./media/sql-database-what-is-a-dtu/bounding-box.png)

Počet jednotek Dtu jsou zvláště užitečná pro pochopení relativní množství prostředků mezi databázemi Azure SQL v různých velikostech výpočetních prostředků a úrovně služeb. Například zdvojnásobení jednotek Dtu zvýšením výpočetní velikost databáze odpovídá zdvojnásobení sady prostředků, které jsou k dispozici pro tuto databázi. Například databáze Premium P11 se 1 750 DTU nabízí 350x více DTU výpočetního výkonu než databáze Basic s 5 DTU.  

Chcete-li získat podrobnější přehled o spotřebě prostředků (DTU) vašich úloh, použijte [dotazu informace o výkonu](sql-database-query-performance.md) na:

- Identifikujte hlavní dotazy podle počtu CPU/doba trvání/spuštění, který může být potenciálně vyladěný za účelem vylepšení výkonu. Například dotaz náročné na vstupně-výstupních operací by mohlo prospět použití [techniky optimalizace v paměti](sql-database-in-memory.md) lepší využití paměti k dispozici na určitou úroveň služby a vypočítat velikost.
- Přejít na podrobnosti o dotazu, zobrazit jeho historii využití prostředků a text.
- Doporučení, které ukazují akce prováděné pro optimalizaci výkonu přístupu [služby SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Elastické jednotky databázové transakce (Edtu)

Spíše než poskytuje vyhrazenou sadu prostředků (Dtu), které nemusí být vždy nezbytné pro SQL Database, která je vždy k dispozici, můžete umístit do databáze [elastického fondu](sql-database-elastic-pool.md) na server služby SQL Database, která sdílí fond prostředků mezi Tyto databáze. Sdílené prostředky v elastickém fondu jsou měřeny pomocí elastic Database Transaction Units nebo Edtu. Elastické fondy poskytují jednoduché nákladově efektivní řešení správy výkonnostních cílů pro více databází s značně liší a nepředvídatelným způsobem. Elastický fond zaručuje, že prostředky nemůže být zpracován jedna databáze ve fondu, zatímco vždy zajištění všem databázím ve fondu má minimální množství potřebné prostředky, které jsou k dispozici.

Fond je uveden stanovený počet jednotek Edtu za stanovenou cenu. V rámci elastického fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci nakonfigurovaných mezí. Databáze při větším zatížení bude spotřebovávat více Edtu, které mají pokrýt poptávku. Databáze pod označením zatížení se spotřebovávají méně Edtu. Databáze s žádné zatížení bude nespotřebovávají žádné Edtu. Zřizování prostředků pro celý fond, spíše než jednotlivé databáze, jsou úlohy správy zjednodušené, poskytuje předvídatelný rozpočet pro fond.

Další eDTU lze do existujícího fondu přidat bez jakéhokoli výpadku databáze a bez jakéhokoli vlivu na databáze ve fondu. Podobně platí, že pokud již přidané eDTU nejsou potřebné, lze je z existujícího fondu kdykoli odebrat. Můžete přičíst nebo odečíst databáze ve fondu nebo omezit množství Edtu, které databáze můžete použít v případě velkého zatížení a vyhradit tak Edtu pro ostatní databáze. Pokud databáze je předvídatelně využívá prostředky, můžete ji odebrat z fondu a nakonfigurovat jako izolovanou databázi s předvídatelným množstvím požadovaných prostředků.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Určit počet Dtu potřebný podle úloh

Pokud jste v úmyslu migrovat existující místní nebo úloha virtuálního počítače SQL serveru do služby Azure SQL Database, můžete použít [kalkulačku DTU](http://dtucalculator.azurewebsites.net/) k odhadu počtu potřebných Dtu. Pro existující úlohy Azure SQL Database, můžete použít [přehled o výkonu dotazování](sql-database-query-performance.md) abyste pochopili spotřebu databázových prostředků (Dtu), abyste získali hlubší vhled pro optimalizaci vašich úloh. Můžete také použít [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) zobrazení dynamické správy k zobrazení využití prostředků za poslední hodinu. Můžete také zobrazit katalog [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) zobrazí využití prostředků za posledních 14 dní, ale v méně přesné průměry pět minut.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Úlohy, které využívají samosprávné elastického fondu prostředků

Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi tento vzor charakterizován průměr nízké využití s relativně málo častými nárůsty využití. SQL Database automaticky vyhodnotí historické údaje používání prostředků databází na existujícím serveru SQL Database a doporučí odpovídající konfigurace fondu na webu Azure Portal. Další informace najdete v tématu [Kdy je vhodné používat elastický fond?](sql-database-elastic-pool.md)

## <a name="service-tier-frequently-asked-questions-faq"></a>Úrovně služeb – nejčastější dotazy (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Je nutné nastavit aplikaci offline převést z databáze založený na DTU pro vrstvu služby založený na virtuálních jádrech

Nové úrovně služeb nabízejí jednoduchý způsob online převodu, který je obdobou stávajícího procesu upgradu databází z úrovně služeb Standard na Premium (a naopak). Tento převod lze inicializovat pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, T-SQL nebo rozhraní REST API. Zobrazit [Správa izolovaných databází](sql-database-single-database-scale.md) a [Správa elastických fondů](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Můžu převést databázi z vrstvy služby s využitím virtuálních jader na jeden založený na DTU

Ano, můžete snadno převést databázi do jakékoli objective podporované výkonu pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, T-SQL nebo rozhraní REST API. Zobrazit [Správa izolovaných databází](sql-database-single-database-scale.md) a [Správa elastických fondů](sql-database-elastic-pool.md).

### <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Můžete upgradovat nebo downgradovat mezi úrovněmi služeb pro obecné účely a pro důležité obchodní informace

Ano, s určitými omezeními. Cíl SKU, musí splňovat maximální databáze nebo elastického fondu velikost, které jste nakonfigurovali pro vaše stávající nasazení. Pokud používáte [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/), SKU pro důležité obchodní je dostupná jenom pro zákazníky s licencemi Enterprise Edition. Pouze zákazníci, kteří migrovat z místního na úrovni general purpose služby pomocí programu zvýhodněné hybridní využití Azure pro SQL Server s licencemi Enterprise Edition, můžete upgradovat na obchodní vrstvu služby. Podrobnosti najdete v tématu [jaké jsou konkrétní práva zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/)?

Tento převod nemá za následek výpadek a lze inicializovat pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure, T-SQL nebo rozhraní REST API. Zobrazit [Správa izolovaných databází](sql-database-single-database-scale.md) a [Správa elastických fondů](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Další postup

- Nákupní model založený na virtuálních jádrech najdete v části [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md)
- Nákupní model založený na DTU najdete v části [nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
