---
title: Azure SQL Database modely nákupu | Dokumentace Microsoftu
description: Další informace o zakoupení modelu pro službu Azure SQL Database.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: f40fe3da6874d8656c9c0a0ddce9fed602cb25f9
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091911"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Azure SQL Database nákupu modely a prostředky 

Logické servery v [Azure SQL Database](sql-database-technical-overview.md) nabízí dva modely nákupu pro výpočty, úložiště a vstupně-VÝSTUPNÍCH prostředků: nákupní model založený na DTU a nákupní model založený na virtuálních jádrech. 

> [!NOTE]
> [Spravované instance](sql-database-managed-instance.md) ve službě Azure SQL Database pouze nabízejí nákupní model založený na virtuálních jádrech.

Následující tabulku a graf porovnání a kontrast tyto dva modely nákupu.

> [!IMPORTANT]
> Nákupní model založený na virtuálních jádrech najdete v části [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md)

|**Nákupní model**|**Popis**|**Nejlepší pro**|
|---|---|---|
|Model na základě DTU|Tento model je založen na připojené míře výpočetní prostředky, úložiště a vstupně-VÝSTUPNÍCH prostředků. Úrovně výkonu se vyjadřují v jednotkách transakcí databáze (DTU) pro samostatné databáze a jednotkách transakcí elastické databáze (eDTU) pro elastické fondy. Další informace o jednotkách Dtu a Edtu najdete v tématu [co jsou jednotky Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?|Nejvhodnější pro zákazníky, kteří chtějí jednoduché, předem nakonfigurované možnosti prostředku.| 
|Model na základě virtuálních jader|Tento model umožňuje nezávisle škálovat výpočetní prostředky a prostředky úložiště. Také umožňuje používat zvýhodněné hybridní využití Azure pro SQL Server k získání úspory nákladů.|Nejvhodnější pro zákazníky, kteří hodnota flexibilitu, řízení a transparentnost.|
||||  

![cenový model](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>nákupní model založený na virtuálních jádrech 

Virtuální jádro reprezentuje logický procesor nabízený s možností volby mezi generacemi hardwaru. Nákupní model založený na virtuálních jádrech poskytuje flexibilitu, kontrolu, transparentnost spotřeby jednotlivých prostředků a jednoduchý způsob převodu místních požadavků na zatížení do cloudu. Tento model umožňuje škálování výpočetních prostředků, paměť a úložiště na základě jejich potřebám provádění úloh. V nákupní model založený na virtuálních jádrech Zákazníci si mohou vybrat mezi úrovní služeb kritické obchodní a obecné účely pro obě [izolované databáze](sql-database-single-database-scale.md) a [elastické fondy](sql-database-elastic-pool.md). 

Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě škálovat výpočetní prostředky a prostředky úložiště, odpovídají zajištění místního výkonu a optimalizovat cena. Pokud vaše databáze nebo elastického fondu využívá víc než 300 DTU převod na vCore může snížit vaše náklady. Můžete převést pomocí rozhraní API podle výběru nebo na webu Azure portal, bez výpadků. Převod však není povinné. Pokud model nákupu DTU splňuje výkonu a obchodních požadavcích, měli byste pokračovat, jeho použití. Pokud se rozhodnete převést z modelu jednotek DTU na modelu virt. jader, by měl vybrat úroveň výkonu pomocí následující pravidlo: vyžaduje minimálně 1 virtuální jádro v úrovni General Purpose; každý 100 DTU na úrovni Standard Každý 125 DTU na úrovni Premium vyžaduje alespoň 1 virtuální jádro v úrovni pro důležité obchodní informace.

V nákupní model založený na virtuálních jádrech zákazníci platí za:
- COMPUTE (úroveň služby a počet virtuálních jader + generace hardwarových) *
- Typ a velikost úložiště dat a protokolů 
- Počet IOs **
- Zálohování úložišť (pro čtení RA-GRS) ** 

\* V počáteční verzi public preview, logické procesory generace 4 jsou založené na Intel E5-2673 v3 (Haswell) 2,4 GHz procesorech.

\*\* Ve verzi preview zálohování a IOs 7 dní je zdarma.

> [!IMPORTANT]
> Výpočetní prostředky, IOs, data a úložiště protokolů se bude fakturovat za databázi nebo elastický fond. Úložiště záloh se účtuje za každou databázi. Podrobné informace o Managed Instance poplatky, najdete v tématu [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> **Omezení oblasti:** nákupní model založený na virtuálních jádrech ještě není k dispozici v následujících oblastech: západní Evropa, Francie – střed, Velká Británie – Jih, Velká Británie – západ a Austrálie – jihovýchod.

## <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

Jednotky propustnosti databáze (DTU) představuje kombinaci měření procesoru, paměti, čte a zapisuje. Nákupní model založený na DTU nabízí sadu předem nakonfigurované sady výpočetních prostředků a zahrnuté úložiště na jednotce různé úrovně výkonu aplikace. Zákazníci, kteří dávají přednost jednoduchosti, kterou nabízejí předem nakonfigurované sady prostředků a pevných plateb každý měsíc, může se stát model založený na DTU více odpovídají jejich potřebám. V nákupní model založený na DTU Zákazníci si mohou vybrat mezi **základní**, **standardní**, a **Premium** úrovně služeb pro obě [izolované databáze](sql-database-single-database-scale.md) a [elastické fondy](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Co jsou jednotky databázové transakce (Dtu)?
Pro izolované databáze Azure SQL na konkrétní úroveň výkonu v rámci [úroveň služby](sql-database-single-database-scale.md), Microsoft zaručuje určité úrovně prostředků pro tuto databázi (nezávisle na jakékoli jiné databáze v cloudu Azure), poskytování předvídatelný úroveň výkonu. Objem prostředků se počítá jako počet jednotek databázové transakce nebo počet jednotek Dtu a je jako součást balíčku míra výpočty, úložiště a vstupně-VÝSTUPNÍCH prostředků. Poměr mezi těmito prostředky byl původně určené [úlohou srovnávacího testu OLTP](sql-database-benchmark-overview.md), navržená tak, aby jako typická pro úlohy OLTP v reálném světě. Pokud vaše úloha překračuje množství některý z těchto prostředků, propustnost je omezené – následek pomalejší výkon a časové limity. Prostředky využívané třídou úlohy nemají vliv na prostředky dostupné pro jiné databáze SQL v cloudu Azure a prostředky využívané třídou jiné úlohy nemají vliv na prostředky dostupné pro vaši službu SQL database.

![ohraničujícího rámečku](./media/sql-database-what-is-a-dtu/bounding-box.png)

Počet jednotek Dtu jsou zvláště užitečná pro pochopení relativní množství prostředků mezi databázemi SQL Azure na různé úrovně výkonu a úrovně služeb. Například zdvojnásobení jednotek Dtu zvýšením úrovně výkonu databáze odpovídá zdvojnásobení sady prostředků, které jsou k dispozici pro tuto databázi. Například databáze Premium P11 se 1 750 DTU nabízí 350x více DTU výpočetního výkonu než databáze Basic s 5 DTU.  

Chcete-li získat podrobnější přehled o spotřebě prostředků (DTU) vašich úloh, použijte [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) na:

- Identifikujte hlavní dotazy podle počtu CPU/doba trvání/spuštění, který může být potenciálně vyladěný za účelem vylepšení výkonu. Například dotaz náročné na vstupně-výstupních operací by mohlo prospět použití [techniky optimalizace v paměti](sql-database-in-memory.md) lepší využití paměti k dispozici na na určité služby vrstvu a úroveň výkonu.
- Přejít na podrobnosti o dotazu, zobrazit jeho historii využití prostředků a text.
- Doporučení, které ukazují akce prováděné pro optimalizaci výkonu přístupu [služby SQL Database Advisor](sql-database-advisor.md).

Můžete změnit [jednotek DTU úrovně služeb](sql-database-service-tiers-dtu.md) kdykoli s minimálními prostoji do vaší aplikace (obecně odstávkou čtyři sekundy). Mnoha firmám a aplikacím stačí vytváření databází a nastavování výkonu na vyžádání, zejména v případě, že jsou vzorce používání relativně předvídatelné. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. Pro tento scénář použijete elastický fond s určitým počtem jednotek Edtu, které jsou odkazy sdíleny mezi více databází ve fondu.

![Úvod do služby SQL Database: DTU izolované databáze podle úrovní](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Co jsou elastické jednotky databázové transakce (Edtu)?
Spíše než poskytuje vyhrazenou sadu prostředků (Dtu), které nemusí být vždy nezbytné pro SQL Database, která je vždy k dispozici, můžete umístit do databáze [elastického fondu](sql-database-elastic-pool.md) na server služby SQL Database, která sdílí fond prostředků mezi Tyto databáze. Sdílené prostředky v elastickém fondu jsou měřeny pomocí elastic Database Transaction Units nebo Edtu. Elastické fondy poskytují jednoduché nákladově efektivní řešení správy výkonnostních cílů pro více databází s značně liší a nepředvídatelným způsobem. Elastický fond zaručuje, že prostředky nemůže být zpracován jedna databáze ve fondu, zatímco vždy zajištění všem databázím ve fondu má minimální množství potřebné prostředky, které jsou k dispozici. 

![Úvod do služby SQL Database: eDTU podle vrstvy a úrovně](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Fond je uveden stanovený počet jednotek Edtu za stanovenou cenu. V rámci elastického fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci nakonfigurovaných mezí. Databáze při větším zatížení bude spotřebovávat více Edtu, které mají pokrýt poptávku. Databáze pod označením zatížení se spotřebovávají méně Edtu. Databáze s žádné zatížení bude nespotřebovávají žádné Edtu. Zřizování prostředků pro celý fond, spíše než jednotlivé databáze, jsou úlohy správy zjednodušené, poskytuje předvídatelný rozpočet pro fond.

Další eDTU lze do existujícího fondu přidat bez jakéhokoli výpadku databáze a bez jakéhokoli vlivu na databáze ve fondu. Podobně platí, že pokud již přidané eDTU nejsou potřebné, lze je z existujícího fondu kdykoli odebrat. Můžete přičíst nebo odečíst databáze ve fondu nebo omezit množství Edtu, které databáze můžete použít v případě velkého zatížení a vyhradit tak Edtu pro ostatní databáze. Pokud databáze je předvídatelně využívá prostředky, můžete ji odebrat z fondu a nakonfigurovat jako izolovanou databázi s předvídatelným množstvím požadovaných prostředků.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Jak lze určit počet DTU potřebných pro určitou úlohu?
Pokud máte v úmyslu migrovat existující úlohu místního počítače nebo virtuálního počítače SQL Server do Azure SQL Database, můžete k odhadnutí potřebného počtu DTU použít [Kalkulačku DTU](http://dtucalculator.azurewebsites.net/). Pro existující úlohy Azure SQL Database, můžete použít [SQL Database Query Performance Insight](sql-database-query-performance.md) abyste pochopili spotřebu databázových prostředků (Dtu), abyste získali hlubší vhled pro optimalizaci vašich úloh. Můžete také použít [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) zobrazení dynamické správy k zobrazení využití prostředků za poslední hodinu. Můžete také zobrazit katalog [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) zobrazí využití prostředků za posledních 14 dní, ale v méně přesné průměry pět minut.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Jak poznám, že by pro mě používání elastického fondu prostředků bylo výhodné?
Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi tento vzor charakterizován průměr nízké využití s relativně málo častými nárůsty využití. SQL Database automaticky vyhodnotí historické údaje používání prostředků databází na existujícím serveru SQL Database a doporučí odpovídající konfigurace fondu na webu Azure Portal. Další informace najdete v tématu [Kdy je vhodné používat elastický fond?](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Co se stane, když dosáhnu maximálního počtu Dtu?
Úrovně výkonu se kalibrují a řídí k poskytnutí prostředků potřebných ke spuštění vaší úlohy databáze až po maximální hodnotu povolenou pro úroveň výkonu vrstvy/vybrané službě. Pokud úloha dosahuje některého z limitů procesory/datový vstupně-výstupních operací/Log vstupně-výstupní operace, budou dál dostávat maximální úroveň prostředků povolený, ale také pravděpodobně nastane latence vyšší dotazu. Tyto limity nevedou k žádným chybám, ale mohou zpomalit zpracování úlohy, pokud se zpomalení nezvýší natolik, že začne docházet k vypršení časového limitu dotazů. Pokud dosáhnete maximální povolených souběžných relací/požadavků uživatelů (pracovních vláken), zobrazí se explicitní chyby. Zobrazit [limity prostředků Azure SQL Database]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) informace o omezení prostředků, které nesouvisí s Procesorem, pamětí, datové v/v nebo transakce protokolovacích v/v.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelace výsledky srovnávacích testů výkonu databáze reálného světa
Je důležité pochopit, že všechny srovnávací testy jsou pouze reprezentativní a orientační. Rychlost transakcí s srovnávací test aplikace nesmí být stejné jako ty, které může dosáhnout s jinými aplikacemi. Test výkonnosti se skládá z kolekce různých transakce typech spustit proti schématu obsahující celou řadu tabulek a datové typy. Během testu využije stejné základní operace, které jsou společné pro všechny úlohy OLTP, nepředstavuje žádné konkrétní třídu databáze nebo aplikace. Cílem testu je poskytovat přiměřenou Průvodce relativní výkon databáze, která může očekávat, že při škálování směrem nahoru nebo dolů mezi úrovněmi výkonu. Ve skutečnosti jsou různé velikosti a složitosti databáze, nastat různé mix úloh a bude odpovídat různými způsoby. Například aplikace náročné na vstupně-výstupní operace narazit na limity vstupně-výstupní operace dříve, nebo aplikace náročné na CPU narazit na omezení procesoru dříve. Neexistuje žádná záruka, že se stejným způsobem jako srovnávací test v rámci zvýšení zatížení výkonu škálovaly jakékoli konkrétní databáze.

Test výkonnosti a jeho metodologie jsou popsány podrobněji níže.

### <a name="benchmark-summary"></a>Přehled srovnávacích testů
ASDB měří výkonnost poměru operací databáze basic, ke kterým dochází nejčastěji v online (OLTP) úloh zpracování transakcí. I když testu je navržená s cloud computingu v paměti, schéma databáze, pro naplnění dat a transakcí byly navrženy široce reprezentativní základní elementy nejčastěji používané úlohy OLTP.

### <a name="schema"></a>Schéma
Schéma zajišťuje mají dostatek různých a složitosti a podporují řadu operací. Test výkonnosti se spustí databázi, který se skládá z šesti tabulky. Tabulky spadají do tří kategorií: pevné velikosti, škálování a stále se rozšiřující. Existují dvě tabulky pevné velikosti; tři horizontální tabulkách. a rostoucí jednu tabulku. Tabulky pevné velikosti mají konstantní počet řádků. Škálování tabulky obsahují kardinalitu, který je úměrný výkon databáze, ale nedojde ke změně během testu. Rostoucí tabulky je velikost jako tabulka škálování na počátečním načtení, ale pak Kardinalita změny při spuštění testu výkonnosti, jako jsou při vkládání a odstraňování řádků.

Schéma obsahuje kombinaci různých typů dat, včetně celé číslo, číselné, znakové a data a času. Schéma obsahuje primární a sekundární klíče, ale ne všechny cizí klíče – to znamená, že jsou bez omezení referenční integrity mezi tabulkami.

Program generování dat generuje data pro výchozí databáze. Data o celé číslo a číselné je vytvořen s různé strategie. V některých případech se náhodně distribuují hodnoty v rozsahu. V ostatních případech sadu hodnot je náhodně permutovanou funkci k zajištění, že konkrétní distribuční zachovaný. Textová pole se generují z vážený seznam slov k vytvoření realistické vypadající data.

Databáze je velikost podle "koeficient." Měřítko (zkracuje SF) určuje Kardinalita škálování a stále se rozšiřující tabulky. Jak je popsáno níže v části Uživatelé a Pacing, velikost databáze, počtu uživatelů a maximální výkon všech škálování poměru mezi sebou.

### <a name="transactions"></a>Transakce
Zatížení se skládá z devíti transakce typech, jak je znázorněno v následující tabulce. Každá transakce je navržen pro zvýraznit konkrétní sadu vlastností systému v databázi modulu a systém hardwaru, s vysokým kontrastem z jiné transakce. Tento přístup usnadňuje posoudit dopad na celkový výkon různé součásti. Například "Čtení Heavy" transakce vytváří velký počet operací čtení z disku.

| Typ transakce | Popis |
| --- | --- |
| Čtení Lite |VYBERTE; v paměti. jen pro čtení |
| Média pro čtení |VYBERTE; většinou v paměti; jen pro čtení |
| Čtení náročná na výkon |VYBERTE; většinou nejsou v paměti; jen pro čtení |
| Aktualizace Lite |AKTUALIZACE; v paměti. čtení a zápis |
| Aktualizovat náročná na výkon |AKTUALIZACE; většinou nejsou v paměti; čtení a zápis |
| Vložit Lite |VLOŽIT. v paměti. čtení a zápis |
| Vložit náročná na výkon |VLOŽIT. většinou nejsou v paměti; čtení a zápis |
| Odstranění |ODSTRANIT; kombinace v paměti a ne v paměti; čtení a zápis |
| Náročná na výkon procesoru |VYBERTE; v paměti. relativně velké zatížení procesoru; jen pro čtení |

### <a name="workload-mix"></a>Kombinace úloh
Transakce jsou z vážená distribuce s následující celkový poměr vybraného náhodně. Celkový poměr je poměr čtení/zápis přibližně 2:1.

| Typ transakce | % poměru |
| --- | --- |
| Čtení Lite |35 |
| Média pro čtení |20 |
| Čtení náročná na výkon |5 |
| Aktualizace Lite |20 |
| Aktualizovat náročná na výkon |3 |
| Vložit Lite |3 |
| Vložit náročná na výkon |2 |
| Odstranění |2 |
| Náročná na výkon procesoru |10 |

### <a name="users-and-pacing"></a>Uživatelé a interval
Úlohou srovnávacího testu vychází z nástroj, který odešle transakce mezi sadu připojení pro simulaci chování počet souběžných uživatelů. I když nejsou všechny transakcí a připojení počítače, generovány, pro jednoduchost označujeme tato připojení "uživatelů." I když každý uživatel pracuje nezávisle všem ostatním uživatelům, všichni uživatelé provádět stejné cyklu kroků dole:

1. Navázání připojení k databázi.
2. Opakujte, dokud signál, ukončete:
   * Vyberte transakce (z náhodně vážená distribuce).
   * Provedení vybrané transakce a měření doby odezvy.
   * Vyčkat, než nemusely zpoždění.
3. Ukončete připojení k databázi.
4. Ukončení.

Nemusely zpoždění (v kroku 2c) je vybrán náhodně, ale s distribuci, která má průměrem 1.0 sekundu. Každý uživatel proto můžete generovat v průměru nejvýše jedna transakce za sekundu.

### <a name="scaling-rules"></a>Škálování pravidla
Počet uživatelů se určuje podle velikosti databáze (v jednotkách škálování dvouúrovňové). Existuje jeden uživatel, za každých pět jednotky škálování dvouúrovňové. Z důvodu nemusely zpoždění může jeden uživatel generovat nejvýše jedna transakce za sekundu, v průměru.

Například-koeficient 500 (SF = 500) databáze bude mít 100 uživatelů a můžete dosáhnout maximální počet 100 TPS. Centrum umožňující prosazovat vyšší TPS míra vyžaduje více uživatelů a větší databáze.

Následující tabulka ukazuje počet uživatelů ve skutečnosti trvalejší pro každou službu vrstvu a úroveň výkonu.

| Úrovně (úrovní výkonu) | Uživatelé | Velikost databáze |
| --- | --- | --- |
| Basic |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2.1 GB |
| Standard (pro S2) |50 |7.1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6) |800 |114 GB |

### <a name="measurement-duration"></a>Měření doby trvání
Platný spuštění testu výkonnosti vyžaduje stálé měření doba trvání aspoň jednu hodinu.

### <a name="metrics"></a>Metriky
Klíčové metriky na test výkonnosti se propustnost a dobu odezvy.

* Propustnost se měří základní výkon v průběhu testu. Propustnost je uveden v transakcí na jednotku of-time, počítací všechny transakce typech.
* Doba odezvy je míra předvídatelnost výkonu. Omezení času odezvy se liší podle třídy služeb, s vyšší třídy služby, která má přísnější požadavky čas odezvy, jak je znázorněno níže.

| Třída služeb | Měření propustnosti | Požadavky na dobu odezvy |
| --- | --- | --- |
| Premium |Transakce za sekundu |95. percentil na 0,5 sekund. |
| Standard |Transakce za minutu |90. percentil na 1.0 sekund. |
| Basic |Transakce za hodinu |80. percentil na 2.0 sekund. |

## <a name="next-steps"></a>Další postup

- Nákupní model založený na virtuálních jádrech najdete v části [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md)
- Nákupní model založený na DTU najdete v části [nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
