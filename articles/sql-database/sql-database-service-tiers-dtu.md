---
title: Úrovně služby Azure SQL Database – DTU | Dokumentace Microsoftu
description: Další informace o úrovních služeb pro zadání jedné a databáze ve fondu k poskytování výpočty velikosti a velikosti úložiště.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 10/04/2018
ms.openlocfilehash: a9e274cea7543fc3361b1f2d0a60fc18176b6248
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831309"
---
# <a name="dtu-based-service-tiers"></a>Úrovně služeb na základě DTU

Na základě jednotek DTU úrovně jsou rozlišené pomocí celou řadu velikostí výpočetních s pevnou velikost zahrnutého úložiště, pevné období uchování zálohy a pevnou cenu. Všechny úrovně služby poskytují flexibilitu změny velikosti výpočetních bez jakýchkoli prostojů. Izolované databáze a elastické fondy se účtují po hodinách na základě úrovně služeb a výpočetního prostředí.

> [!IMPORTANT]
> SQL Database Managed Instance aktuálně ve verzi public preview nepodporuje nákupní model založený na DTU. Další informace najdete v tématu [Azure SQL Database Managed Instance](sql-database-managed-instance.md).

> [!NOTE]
> Informace o úrovních služeb založený na virtuálních jádrech najdete v tématu [úrovně služeb založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). Informace o odlišení těchto služeb na základě jednotek DTU úrovně a úrovní služeb založený na virtuálních jádrech najdete v tématu [modely nákupu Azure SQL Database](sql-database-service-tiers.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Porovnání úrovní služeb na základě DTU

Výběr úrovně služeb závisí primárně na obchodní kontinuity podnikových procesů, úložiště a požadavky na výkon.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|Cílové úlohy|Vývoj a provoz|Vývoj a provoz|Vývoj a provoz||
|Smlouva SLA o provozuschopnosti|99,99 %|99,99 %|99,99 %|Není k dispozici ve verzi preview|
|Uchování záloh|7 dní|po dobu 35 dní|po dobu 35 dní|
|Procesor|Nízká|Nízká, střední, vysoká|Střední, vysoká|
|Vstupně-výstupní propustnost (přibližné) |2.5 vstupně-výstupních operací za DTU| 2.5 vstupně-výstupních operací za DTU | 48 vstupně-výstupních operací za DTU|
|Vstupně-výstupní latence (přibližné)|5 ms (čtení), 10 ms (zápis)|5 ms (čtení), 10 ms (zápis)|2 ms (čtení a zápis)|
|Indexu Columnstore |neuvedeno|S3 a novější|Podporováno|
|OLTP v paměti|neuvedeno|neuvedeno|Podporováno|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Omezení úložiště a jednotek DTU izolované databáze

Výpočetní velikosti se vyjadřují v jednotky transakcí databáze (Dtu) pro izolované databáze a elastické databáze jednotky transakce (Edtu) pro elastické fondy. Další informace o jednotkách Dtu a Edtu najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers.md#dtu-based-purchasing-model)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště | 2 GB | 1 TB | 4 TB  | 
| Maximální počet jednotek Dtu | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Omezení databázi ve fondu, úložiště a eDTU pro elastický fond

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště na databázi  | 2 GB | 1 TB | 1 TB | 
| Maximální velikost úložiště na fond | 156 GB | 4 TB | 4 TB | 
| Maximální počet Edtu na databázi | 5 | 3000 | 4000 | 
| Maximální počet Edtu na fond | 1600 | 3000 | 4000 | 
| Maximální počet databází na fond | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech kromě následujících: západní USA – střed, Čína – východ, USDoDCentral, Německo – střed, USDoDEast, USA (gov) – jihozápad, USGov Iowa, Německo-severovýchod, Čína – sever. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>Srovnávací test DTU

Fyzické charakteristiky (procesoru, paměti, vstupně-výstupních operací) přidružené k každá míra DTU se kalibrují srovnávací test, který simuluje skutečná databázové úlohy.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelace výsledky srovnávacích testů výkonu databáze reálného světa

Je důležité pochopit, že všechny srovnávací testy jsou pouze reprezentativní a orientační. Rychlost transakcí s srovnávací test aplikace nesmí být stejné jako ty, které může dosáhnout s jinými aplikacemi. Test výkonnosti se skládá z kolekce různých transakce typech spustit proti schématu obsahující celou řadu tabulek a datové typy. Během testu využije stejné základní operace, které jsou společné pro všechny úlohy OLTP, nepředstavuje žádné konkrétní třídu databáze nebo aplikace. Cílem testu je poskytovat přiměřenou Průvodce relativní výkon databáze, která může očekávat, že při škálování směrem nahoru nebo dolů mezi velikostí výpočetních. Ve skutečnosti jsou různé velikosti a složitosti databáze, nastat různé mix úloh a bude odpovídat různými způsoby. Například aplikace náročné na vstupně-výstupní operace narazit na limity vstupně-výstupní operace dříve, nebo aplikace náročné na CPU narazit na omezení procesoru dříve. Neexistuje žádná záruka, že se stejným způsobem jako srovnávací test v rámci zvýšení zatížení výkonu škálovaly jakékoli konkrétní databáze.

Test výkonnosti a jeho metodologie jsou popsány podrobněji níže.

### <a name="benchmark-summary"></a>Přehled srovnávacích testů

ASDB měří výkon kombinaci základní databázových operací, které se vyskytují nejčastěji v online (OLTP) úloh zpracování transakcí. I když testu je navržená s cloud computingu v paměti, schéma databáze, pro naplnění dat a transakcí byly navrženy široce reprezentativní základní elementy nejčastěji používané úlohy OLTP.

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
   - Vyberte transakce (z náhodně vážená distribuce).
   - Provedení vybrané transakce a měření doby odezvy.
   - Vyčkat, než nemusely zpoždění.
3. Ukončete připojení k databázi.
4. Ukončení.

Nemusely zpoždění (v kroku 2c) je vybrán náhodně, ale s distribuci, která má průměrem 1.0 sekundu. Každý uživatel proto můžete generovat v průměru nejvýše jedna transakce za sekundu.

### <a name="scaling-rules"></a>Škálování pravidla

Počet uživatelů se určuje podle velikosti databáze (v jednotkách škálování dvouúrovňové). Existuje jeden uživatel, za každých pět jednotky škálování dvouúrovňové. Z důvodu nemusely zpoždění může jeden uživatel generovat nejvýše jedna transakce za sekundu, v průměru.

Například-koeficient 500 (SF = 500) databáze bude mít 100 uživatelů a můžete dosáhnout maximální počet 100 TPS. Centrum umožňující prosazovat vyšší TPS míra vyžaduje více uživatelů a větší databáze.

### <a name="measurement-duration"></a>Měření doby trvání

Platný spuštění testu výkonnosti vyžaduje stálé měření doba trvání aspoň jednu hodinu.

### <a name="metrics"></a>Metriky

Klíčové metriky na test výkonnosti se propustnost a dobu odezvy.

- Propustnost se měří základní výkon v průběhu testu. Propustnost je uveden v transakcí na jednotku of-time, počítací všechny transakce typech.
- Doba odezvy je míra předvídatelnost výkonu. Omezení času odezvy se liší podle třídy služeb, s vyšší třídy služby, která má přísnější požadavky čas odezvy, jak je znázorněno níže.

| Třída služeb | Měření propustnosti | Požadavky na dobu odezvy |
| --- | --- | --- |
| Premium |Transakce za sekundu |95. percentil na 0,5 sekund. |
| Standard |Transakce za minutu |90. percentil na 1.0 sekund. |
| Basic |Transakce za hodinu |80. percentil na 2.0 sekund. |

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro izolované databáze, najdete v části [omezení prostředků založený na DTU databáze SQL pro izolované databáze](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro elastické fondy, najdete v části [omezení prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
