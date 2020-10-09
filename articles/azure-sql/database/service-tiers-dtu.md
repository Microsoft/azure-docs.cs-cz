---
title: Úrovně služeb – model nákupu na základě DTU
description: Přečtěte si o úrovních služeb v modelu nákupu založeném na DTU pro Azure SQL Database k zajištění výpočetních a úložných velikostí.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 10/07/2020
ms.reviewer: ''
ms.openlocfilehash: 8ed4edb8739758af057276bd21c4ad62bf9ab974
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91848853"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Úrovně služby v nákupním modelu založeném na DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Úrovně služeb v modelu nákupu na základě DTU jsou rozlišené rozsahem výpočetních velikostí s pevným množstvím zahrnutého úložiště, pevným obdobím uchovávání záloh a pevnou cenou. Všechny úrovně služeb v modelu nákupu založeném na DTU poskytují flexibilitu při změně výpočetních velikostí s minimálními [výpadky](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/). v průběhu období ale dojde k přepnutí připojení do databáze po krátkou dobu, kterou je možné zmírnit pomocí logiky opakování. Samostatné databáze a elastické fondy se účtují po hodinách na základě úrovně služby a výpočetní velikosti.

> [!IMPORTANT]
> [Spravovaná instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) nepodporuje nákupní model založený na DTU. 


> [!NOTE]
> Informace o úrovních služeb založených na vCore najdete v tématu [úrovně služeb založené na Vcore](service-tiers-vcore.md). Informace o odlišení úrovní služeb založených na DTU a úrovních služeb založených na vCore najdete v tématu [nakupování modelů](purchasing-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Porovnání úrovní služeb založených na DTU

Výběr úrovně služeb závisí hlavně na požadavcích na provozní kontinuitu, úložiště a výkon.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|**Cílové zatížení**|Vývoj a produkce|Vývoj a produkce|Vývoj a produkce|
|**Smlouva SLA pro dobu provozu**|99,99 %|99,99 %|99,99 %|
|**Maximální uchovávání záloh**|7 dní|35 dní|35 dní|
|**Procesor**|Nízká|Nízká, střední, vysoká|Střední, vysoká|
|**IOPS (přibližná)**\* |1-5 IOPS na DTU| 1-5 IOPS na DTU | 25 IOPS na DTU|
|**Latence v/v (přibližná)**|5 ms (čtení), 10 ms (zápis)|5 ms (čtení), 10 ms (zápis)|2 ms (čtení a zápis)|
|**Indexování columnstore** |Není k dispozici|S3 a vyšší|Podporováno|
|**OLTP v paměti**|N/A|N/A|Podporováno|

\* Všechny vstupně-výstupní operace čtení a zápisu proti datovým souborům, včetně/v v/v (kontrolní bod a opožděný zápis)

> [!IMPORTANT]
> Cíle služeb Basic, S0, S1 a S2 poskytují méně než jeden vCore (CPU).  Pro úlohy náročné na procesor se doporučuje cíl služby S3 nebo vyšší. 
>
> V cílech služeb Basic, S0 a S1 se soubory databáze ukládají ve službě Azure Storage úrovně Standard, která využívá úložná média založená na pevných discích (HDD). Tyto cíle služeb jsou nejvhodnější pro vývoj, testování a jiné často používané úlohy, které jsou méně citlivé na variabilitu výkonu.
>

> [!TIP]
> Pokud chcete zobrazit skutečná omezení [zásad správného řízení prostředků](resource-limits-logical-server.md#resource-governance) pro databázi nebo elastický fond, Dotazujte zobrazení [Sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

> [!NOTE]
> Pokud chcete prozkoumat Azure, můžete získat bezplatnou databázi v Azure SQL Database v základní úrovni služby ve spojení s bezplatným účtem Azure. Informace najdete v tématu [Vytvoření spravované cloudové databáze pomocí bezplatného účtu Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>DTU a omezení úložiště pro izolovanou databázi

Velikosti výpočetních hodnot se vyjadřují v souvislosti s jednotkami DTU (Database Transaction units) pro izolované databáze a jednotkami elastické databázové transakce (eDTU) pro elastické fondy. Další informace o DTU a eDTU najdete v tématu [nákupní model založený na DTU](purchasing-models.md#dtu-based-purchasing-model).

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| **Maximální velikost úložiště** | 2 GB | 1 TB | 4 TB  |
| **Maximální DTU** | 5 | 3000 | 4000 |

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Omezení eDTU elastického fondu, úložiště a databáze ve fondu

|| **Basic** | **Standardní** | **Premium** |
| :-- | --: | --: | --: |
| **Maximální velikost úložiště na databázi**  | 2 GB | 1 TB | 1 TB |
| **Maximální velikost úložiště na fond** | 156 GB | 4 TB | 4 TB |
| **Maximální počet eDTU na databázi** | 5 | 3000 | 4000 |
| **Maximální počet eDTU na fond** | 1600 | 3000 | 4000 |
| **Maximální počet databází na fond** | 500  | 500 | 100 |

> [!IMPORTANT]
> Ve všech oblastech je aktuálně k dispozici více než 1 TB úložiště na úrovni Premium s výjimkou: Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod. V těchto oblastech je maximální velikost úložiště na úrovni Premium omezená na 1 TB.  Další informace najdete v tématu [aktuální omezení P11-P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

## <a name="dtu-benchmark"></a>Srovnávací test DTU

Fyzické vlastnosti (CPU, paměť, vstupně-výstupní operace) přidružené ke každé míře DTU jsou kalibrovány pomocí srovnávacího testu, který simuluje reálné zatížení databáze.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelace výsledků srovnávacích testů se skutečným výkonem databáze

Je důležité pochopit, že všechny srovnávací testy jsou pouze reprezentativní a informativní. Sazby za transakce dosažené v rámci aplikace srovnávacích testů nebudou stejné jako ty, které by se mohly dosáhnout u jiných aplikací. Srovnávací test zahrnuje kolekci různých typů transakcí spouštěných proti schématu obsahujícímu rozsah tabulek a datových typů. I když srovnávací test prosazuje stejné základní operace, které jsou společné pro všechny OLTP úlohy, nepředstavuje žádnou konkrétní třídu databáze ani aplikace. Cílem srovnávacího testu je poskytnout přiměřenou příručku k relativnímu výkonu databáze, která může být očekávána při vertikálním navýšení nebo snížení kapacity výpočetních velikostí. Ve skutečnosti jsou databáze různých velikostí a složitosti, se kterými se setká různé úlohy a reagují různými způsoby. Například aplikace náročná na vstupně-výstupní operace může vyhodnotit mezní hodnoty vstupně-výstupních operací dřív nebo aplikace náročné na procesor může vyhodnotit PROCESORy dřív. Neexistuje žádná záruka, že se všechny konkrétní databáze budou škálovat stejným způsobem jako srovnávací testy v rámci zvýšení zátěže.

Srovnávací test a jeho metodika jsou podrobněji popsány níže.

### <a name="benchmark-summary"></a>Souhrn srovnávacího testu

Srovnávací test měří výkon kombinace základních databázových operací, ke kterým dochází nejčastěji v úlohách OLTP (online Transaction Processing). I když je srovnávací test navržený s využitím cloud computingu, je schéma databáze, naplnění dat a transakce navrženo tak, aby bylo široké zástupné základní prvky nejčastěji používané v OLTP úlohách.

### <a name="schema"></a>Schéma

Schéma je navrženo tak, aby mělo dostatek různých a složitých operací pro podporu široké škály operací. Srovnávací testy se spouští na databázi skládající se ze šesti tabulek. Tabulky spadají do tří kategorií: pevná velikost, škálování a rostoucí hodnota. Existují dvě tabulky s pevnou velikostí. tři tabulky škálování; a jedna narůstající tabulka. Tabulky pevné velikosti mají konstantní počet řádků. Tabulky škálování mají mohutnost, která je úměrná výkonu databáze, ale během srovnávacího testu se nemění. Rostoucí tabulka má velikost jako tabulka škálování při počátečním zatížení, ale v průběhu spuštění srovnávacího testu se změny mohutnosti při vložení a odstranění řádků.

Schéma obsahuje kombinaci datových typů, včetně celého čísla, číselného, znaku a data a času. Schéma zahrnuje primární a sekundární klíče, ale ne žádné cizí klíče – to znamená, že mezi tabulkami neexistují žádná omezení referenční integrity.

Program pro generování dat generuje data pro počáteční databázi. Celočíselná a číselná data se generují s různými strategiemi. V některých případech jsou hodnoty náhodně rozloženy přes určitý rozsah. V ostatních případech se sada hodnot náhodně permuted, aby se zajistilo, že se zachová konkrétní distribuce. Textová pole se generují z váženého seznamu slov, aby se vytvořila reálná data hledání.

Databáze má velikost na základě "faktoru škálování". Faktor škálování (zkrácený jako SF) určuje mohutnost škálování a rostoucích tabulek. Jak je popsáno níže v části Uživatelé a stimulace, velikost databáze, počet uživatelů a maximální výkon v poměru k sobě.

### <a name="transactions"></a>Transakce

Zatížení se skládá z devíti typů transakcí, jak je znázorněno v následující tabulce. Každá transakce je navržena k zdůraznění konkrétní sady systémových vlastností v databázovém stroji a na systémovém hardwaru s vysokým kontrastem od ostatních transakcí. Tento přístup usnadňuje vyhodnocení dopadu různých komponent na celkový výkon. Například transakce "Read těžký" vytváří velký počet operací čtení z disku.

| Transaction Type (Typ transakce) | Popis |
| --- | --- |
| Přečíst Lite |VYBRALI v paměti; jen pro čtení |
| Přečíst médium |VYBRALI hlavně v paměti; jen pro čtení |
| Čtení těžkých |VYBRALI hlavně není v paměti; jen pro čtení |
| Aktualizace Lite |Update v paměti; čtení i zápis |
| Aktualizace těžkých |Update hlavně není v paměti; čtení i zápis |
| Vložit Lite |ZADAT v paměti; čtení i zápis |
| Vložit těžký |ZADAT hlavně není v paměti; čtení i zápis |
| Delete |DSTRANIT kombinace v paměti a nikoli v paměti; čtení i zápis |
| Vysoký procesor |VYBRALI v paměti; poměrně silné zatížení procesoru; jen pro čtení |

### <a name="workload-mix"></a>Kombinace úloh

Transakce se vyberou náhodně z vážené distribuce s následující celkovou kombinací. Celková kombinace má poměr pro čtení a zápis přibližně 2:1.

| Transaction Type (Typ transakce) | % Kombinace |
| --- | --- |
| Přečíst Lite |35 |
| Přečíst médium |20 |
| Čtení těžkých |5 |
| Aktualizace Lite |20 |
| Aktualizace těžkých |3 |
| Vložit Lite |3 |
| Vložit těžký |2 |
| Delete |2 |
| Vysoký procesor |10 |

### <a name="users-and-pacing"></a>Uživatelé a stimulace

Úlohy srovnávacích testů se řídí nástrojem, který odesílá transakce napříč sadou připojení, aby simulovala chování několika souběžných uživatelů. I když jsou všechna připojení a transakce generované počítačem, pro zjednodušení odkazujeme na tato připojení jako na "uživatelé". I když každý uživatel pracuje nezávisle na všech ostatních uživatelích, provede všechny uživatele stejný cyklus kroků uvedených níže:

1. Navažte připojení k databázi.
2. Opakujte, dokud se signál neukončí:
   - Vyberte transakci náhodně (z vážené distribuce).
   - Proveďte vybranou transakci a změřte dobu odezvy.
   - Počkejte na stimulace zpoždění.
3. Zavřete připojení k databázi.
4. Akci.

Zpoždění stimulace (v kroku 2C) je vybráno náhodně, ale s distribucí s průměrem 1,0 sekund. Každý uživatel tedy může v průměru vygenerovat maximálně jednu transakci za sekundu.

### <a name="scaling-rules"></a>Pravidla škálování

Počet uživatelů je určený velikostí databáze (v jednotkách se škálováním na více zdrojů). Pro každých pět jednotek se škálováním na více zdrojů existuje jeden uživatel. Kvůli zpoždění stimulace může jeden uživatel vygenerovat maximálně jednu transakci za sekundu v průměru.

Například škálování faktoru 500 (SF = 500) bude mít uživatele 100 a může dosáhnout maximální míry 100 TPS. Aby bylo možné zvýšit TPS míru, vyžaduje více uživatelů a větší databázi.

### <a name="measurement-duration"></a>Doba trvání měření

Platný běh srovnávacího testu vyžaduje dobu trvání měření ustáleného stavu nejméně jednu hodinu.

### <a name="metrics"></a>Metriky

Klíčové metriky v srovnávacím testu jsou propustnost a doba odezvy.

- Propustnost je základní míra výkonu v srovnávacím testu. Propustnost je hlášena v transakcích za jednotku času a počítá všechny typy transakcí.
- Doba odezvy je míra předvídatelnosti výkonu. Omezení doby odezvy se liší u třídy služby s vyššími třídami služby s přísnějším požadavkem na dobu odezvy, jak je znázorněno níže.

| Třída služby | Míra propustnosti | Doba odezvy – požadavek |
| --- | --- | --- |
| Premium |Transakcí za sekundu |95. percentil v 0,5 sekundách |
| Standard |Transakcí za minutu |90. percentil v 1,0 sekundách |
| Základní |Transakcí za hodinu |80th percentil v 2,0 sekundách |

## <a name="next-steps"></a>Další kroky

- Podrobnosti o specifických velikostech výpočtů a možnostech velikosti úložiště, které jsou dostupné pro jednotlivé databáze, najdete v tématu [SQL Database omezení prostředků na základě DTU pro jednotlivé databáze](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Podrobnosti o specifických velikostech výpočtů a možnostech velikosti úložiště dostupných pro elastické fondy najdete v tématu [SQL Database omezení prostředků na základě DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
