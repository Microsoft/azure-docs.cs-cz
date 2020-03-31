---
title: Úrovně služeb – nákupní model založený na DTU
description: Další informace o úrovních služeb v modelu nákupu založeném na DTU pro jednu a sdružené databáze pro poskytování velikostí výpočetních prostředků a úložiště.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/26/2019
ms.openlocfilehash: 2f316e57e407a0588e77f56d6e1fbe8c19ba5fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75562115"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Úrovně služby v nákupním modelu založeném na DTU

Úrovně služeb v nákupním modelu založeném na DTU se liší řadou výpočetních velikostí s pevnou částkou zahrnutého úložiště, pevnou dobou uchování pro zálohování a pevnou cenou. Všechny úrovně služeb v nákupním modelu založeném na DTU poskytují flexibilitu při změně velikosti výpočetních prostředků s minimálními [prostoji](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/); však je přepínač přes období, kdy dojde ke ztrátě připojení k databázi na krátkou dobu, které lze zmírnit pomocí logiky opakování. Jednotlivé databáze a elastické fondy se účtují každou hodinu na základě úrovně služeb a výpočetní velikosti.

> [!IMPORTANT]
> Spravovaná instance SQL Database nepodporuje nákupní model založený na DTU. Další informace najdete v tématu [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> [!NOTE]
> Informace o úrovních služeb založených na virtuálních jádrech najdete v [tématu úrovně služeb založené na virtuálních jádrech](sql-database-service-tiers-vcore.md). Informace o rozlišování úrovní služeb založených na DTU a úrovních služeb založených na virtuálních jádrech najdete v [tématu Nákupní modely Azure SQL Database](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Porovnání úrovní služeb založených na DTU

Výběr úrovně služeb závisí především na požadavcích na kontinuitu provozu, úložiště a výkon.

||Basic|Standard|Premium|
| :-- | --: |--:| --:|
|Cílové pracovní zatížení|Vývoj a výroba|Vývoj a výroba|Vývoj a výroba|
|Doba sla|99,99 %|99,99 %|99,99 %|
|Maximální uchovávání záloh|7 dní|35 dní|35 dní|
|Procesor|Nízká|Nízká, Střední, Vysoká|Střední, Vysoká|
|Propustnost vo (přibližná) |1-5 IOPS na DTU| 1-5 IOPS na DTU | 25 VOPS na DTU|
|Latence vi (přibližná)|5 ms (čtení), 10 ms (zápis)|5 ms (čtení), 10 ms (zápis)|2 ms (čtení/zápis)|
|Columnstore indexování |Není dostupné.|S3 a vyšší|Podporuje se|
|OLTP v paměti|Není dostupné.|Není dostupné.|Podporuje se|
|||||

> [!IMPORTANT]
> Úrovně služeb Basic, Standard S0, S1 a S2 poskytují méně než jeden virtuální jádro (CPU).  Pro úlohy náročné na procesor se doporučuje úroveň služeb S3 nebo vyšší. 
>
>Pokud jde o ukládání dat, úrovně služeb Basic, Standard S0 a S1 jsou umístěny na objektech BLOB standardní stránky. Objekty BLOB se standardní stránkou používají paměťová média založená na pevném disku (HDD) a jsou nejvhodnější pro vývoj, testování a další zřídka přístupné úlohy, které jsou méně citlivé na variabilitu výkonu.
>

> [!NOTE]
> Bezplatnou databázi Azure SQL můžete získat na úrovni základní služby ve spojení s bezplatným účtem Azure a prozkoumat Azure. Další informace najdete [v tématu Vytvoření spravované cloudové databáze pomocí bezplatného účtu Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Limity DTU pro jednu databázi a úložiště

Výpočetní velikosti jsou vyjádřeny z hlediska jednotek transakcí databáze (DTU) pro jednotlivé databáze a elastické jednotky transakcí databáze (eDTU) pro elastické fondy. Další informace o dtu a eDTU najdete v [tématu nákupní model založený na DTU](sql-database-purchase-models.md#dtu-based-purchasing-model).

||Basic|Standard|Premium|
| :-- | --: | --: | --: |
| Maximální velikost úložiště | 2 GB | 1 TB | 4 TB  |
| Maximální DTUs | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>EDTU eDTU, úložiště a limity sdružené databáze v eDTU, úložiště a sdružené databáze

| | **Basic** | **Standard** | **Premium** |
| :-- | --: | --: | --: |
| Maximální velikost úložiště na databázi  | 2 GB | 1 TB | 1 TB |
| Maximální velikost úložiště na fond | 156 GB | 4 TB | 4 TB |
| Maximální počet eDTU na databázi | 5 | 3000 | 4000 |
| Maximální počet eDTU na fond | 1600 | 3000 | 4000 |
| Maximální počet databází na fond | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je v současné době k dispozici ve všech oblastech kromě: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Západní střed USA, USA DoD a Us Government Central. V těchto oblastech je maximální úložiště na úrovni Premium omezeno na 1 TB.  Další informace naleznete v [aktuálních omezeních P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>Měřítko DTU

Fyzické vlastnosti (CPU, paměť, IO) přidružené ke každému měření DTU jsou kalibrovány pomocí srovnávacího testu, který simuluje zatížení databáze v reálném světě.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelování výsledků srovnávacích testů s výkonností databáze v reálném světě

Je důležité si uvědomit, že všechna referenční hodnoty jsou pouze reprezentativní a orientační. Transakční sazby dosažené referenční aplikací nebudou stejné jako sazby, kterých by mohlo být dosaženo u jiných aplikací. Referenční hodnota zahrnuje kolekci různých typů transakcí spuštěných proti schématu obsahujícímu řadu tabulek a datových typů. Zatímco benchmark provádí stejné základní operace, které jsou společné pro všechny úlohy OLTP, nepředstavuje žádnou konkrétní třídu databáze nebo aplikace. Cílem srovnávacího testu je poskytnout přiměřené vodítko pro relativní výkon databáze, které lze očekávat při škálování nahoru nebo dolů mezi výpočetní velikosti. Ve skutečnosti jsou databáze různých velikostí a složitosti, setkávají se s různými kombinacemi úloh a budou reagovat různými způsoby. Například aplikace náročné na vi může zasáhnout prahové hodnoty vi dříve nebo aplikace náročné na procesor může zasáhnout limity procesoru dříve. Neexistuje žádná záruka, že konkrétní databáze bude škálovat stejným způsobem jako referenční hodnota při zvýšení zatížení.

Referenční hodnota a její metodika jsou podrobněji popsány níže.

### <a name="benchmark-summary"></a>Souhrn srovnávacího testu

Srovnávací test měří výkon kombinace základních databázových operací, ke kterým dochází nejčastěji v úlohách zpracování online transakcí (OLTP). Přestože je srovnávací test navržen s ohledem na cloud computing, schéma databáze, populace dat a transakce byly navrženy tak, aby byly široce reprezentativní pro základní prvky, které se nejčastěji používají v úlohách OLTP.

### <a name="schema"></a>Schéma

Schéma je navrženo tak, aby bylo dostatečně rozlišené a složité pro podporu široké škály operací. Benchmark běží proti databázi složené ze šesti tabulek. Tabulky spadají do tří kategorií: pevná velikost, změna měřítka a růst. Existují dvě tabulky pevné velikosti; tři tabulky měřítka; a jeden rostoucí stůl. Tabulky s pevnou velikostí mají konstantní počet řádků. Škálování tabulky mají mohutnost, která je úměrná výkonu databáze, ale nezmění během benchmarku. Rostoucí tabulka je velikost jako škálování tabulky na počáteční zatížení, ale pak mohutnost změny v průběhu spuštění benchmark jako řádky jsou vloženy a odstraněny.

Schéma obsahuje kombinaci datových typů, včetně celéčíslo, číselné, znak a datum a čas. Schéma obsahuje primární a sekundární klíče, ale ne žádné cizí klíče – to znamená, že neexistují žádná omezení referenční integrity mezi tabulkami.

Program generování dat generuje data pro počáteční databázi. Celá čísla a číselná data jsou generována s různými strategiemi. V některých případech jsou hodnoty distribuovány náhodně v rozsahu. V ostatních případech je sada hodnot náhodně permutována, aby bylo zajištěno, že je zachováno určité rozdělení. Textová pole jsou generována z váženého seznamu slov, aby se vytvořila realisticky vypadající data.

Velikost databáze je na základě "faktor měřítka.". Faktor měřítka (zkráceně SF) určuje mohutnost škálování a růst tabulek. Jak je popsáno níže v části Uživatelé a tempo, velikost databáze, počet uživatelů a maximální výkon všech měřítko v poměru k sobě navzájem.

### <a name="transactions"></a>Transakce

Pracovní vytížení se skládá z devíti typů transakcí, jak je znázorněno v tabulce níže. Každá transakce je navržena tak, aby zvýraznila určitou sadu vlastností systému v databázovém stroji a systémovém hardwaru s vysokým kontrastem od ostatních transakcí. Tento přístup usnadňuje posouzení dopadu různých součástí na celkový výkon. Například transakce "Čtení heavy" vytváří významný počet operací čtení z disku.

| Transaction Type (Typ transakce) | Popis |
| --- | --- |
| Přečtěte si Lite |VYBERTE; v paměti; jen pro čtení |
| Čtení média |VYBERTE; většinou v paměti; jen pro čtení |
| Přečtěte si těžké |VYBERTE; většinou není v paměti; jen pro čtení |
| Aktualizace Lite |AKTUALIZACE; v paměti; čtení a zápis |
| Aktualizovat těžké |AKTUALIZACE; většinou není v paměti; čtení a zápis |
| Vložit lite |Vložte; v paměti; čtení a zápis |
| Vložit těžké |Vložte; většinou není v paměti; čtení a zápis |
| Odstranění |ODSTRANIT; kombinace v paměti a ne v paměti; čtení a zápis |
| TĚŽKÝ PROCESOR |VYBERTE; v paměti; poměrně vysoké zatížení CPU; jen pro čtení |

### <a name="workload-mix"></a>Kombinace pracovních vytížení

Transakce jsou náhodně vybrány z váženého rozdělení s následujícím celkovým mixem. Celkový mix má poměr čtení a zápisu přibližně 2:1.

| Transaction Type (Typ transakce) | % směsi |
| --- | --- |
| Přečtěte si Lite |35 |
| Čtení média |20 |
| Přečtěte si těžké |5 |
| Aktualizace Lite |20 |
| Aktualizovat těžké |3 |
| Vložit lite |3 |
| Vložit těžké |2 |
| Odstranění |2 |
| TĚŽKÝ PROCESOR |10 |

### <a name="users-and-pacing"></a>Uživatelé a tempo

Úloha benchmarku je řízena z nástroje, který odesílá transakce přes sadu připojení k simulaci chování několika souběžných uživatelů. Přestože jsou všechna připojení a transakce generovány počítačem, pro jednoduchost označujeme tato připojení jako "uživatelé". Ačkoli každý uživatel pracuje nezávisle na všech ostatních uživatelích, všichni uživatelé provádějí stejný cyklus kroků uvedených níže:

1. Navázání připojení k databázi.
2. Opakujte, dokud není signalizováno ukončení:
   - Vyberte transakci náhodně (z váženého rozdělení).
   - Proveďte vybranou transakci a změřte dobu odezvy.
   - Počkejte na zpoždění.
3. Zavřete připojení k databázi.
4. Ukončit.

Zpoždění tempo (v kroku 2c) je vybráno náhodně, ale s rozdělením, které má průměr 1,0 sekundy. Každý uživatel tak může v průměru generovat maximálně jednu transakci za sekundu.

### <a name="scaling-rules"></a>Změna velikosti pravidel

Počet uživatelů je určen velikostí databáze (v jednotkách faktoru měřítka). Pro každých pět jednotek s faktorem měřítka je jeden uživatel. Z důvodu zpoždění přecházení může jeden uživatel generovat v průměru maximálně jednu transakci za sekundu.

Například měřítko 500 (SF = 500) databáze bude mít 100 uživatelů a může dosáhnout maximální rychlost 100 TPS. Chcete-li řídit vyšší rychlost TPS vyžaduje více uživatelů a větší databáze.

### <a name="measurement-duration"></a>Doba měření

Platné spuštění srovnávacího testu vyžaduje dobu měření v ustáleném stavu nejméně jednu hodinu.

### <a name="metrics"></a>Metriky

Klíčovými metrikami v benchmarku jsou propustnost a doba odezvy.

- Propustnost je základním měřítkem výkonu v benchmarku. Propustnost je vykazována v transakcích za jednotku času, včetně všech typů transakcí.
- Doba odezvy je měřítkem předvídatelnosti výkonu. Omezení doby odezvy se liší podle třídy služby, přičemž vyšší třídy služeb mají přísnější požadavek na dobu odezvy, jak je znázorněno níže.

| Třída služeb | Míra propustností | Požadavek na dobu odezvy |
| --- | --- | --- |
| Premium |Transakce za sekundu |95. percentil při 0,5 sekundě |
| Standard |Transakce za minutu |90. percentil za 1,0 sekundy |
| Basic |Transakce za hodinu |80. percentil za 2,0 sekundy |

## <a name="next-steps"></a>Další kroky

- Podrobnosti o konkrétních výpočetních velikostech a možnostech velikosti úložiště, které jsou k dispozici pro jednotlivé databáze, naleznete [v tématu limity prostředků založených na databázi SQL Database pro jednotlivé databáze](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Podrobnosti o konkrétních velikostech výpočetních prostředků a možnostech velikosti úložiště, které jsou k dispozici pro elastické fondy, naleznete [v tématu limity prostředků založených na databázi SQL Database DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
