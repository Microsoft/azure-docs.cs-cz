---
title: "Přehled služby Azure SQL Database srovnávacího testu"
description: "Toto téma popisuje databáze Azure SQL, srovnávací test měření výkonu databáze SQL Azure."
services: sql-database
author: jan-eng
manager: jhubbard
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 06/21/2016
ms.author: janeng
ms.openlocfilehash: 25685f663d976674ad877bcfa1409ef016dd02ae
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-benchmark-overview"></a>Přehled služby Azure SQL Database srovnávacího testu
## <a name="overview"></a>Přehled
Microsoft Azure SQL Database nabízí tři [úrovních služeb](sql-database-service-tiers.md) s více úrovněmi výkonu. Každá úroveň výkonu poskytuje roste sadu prostředků, nebo "power" navržený tak, aby stále vyšší propustnost.

Je důležité, abyste mohli vyčíslení jak roste sílu každou úroveň výkonu překládá do databáze vyšší výkon. Uděláte to této společnost Microsoft vyvinula srovnávacího testu pro databáze SQL Azure (ASDB). Testu výkonnosti vykonává směs základní operace v všechny úlohy OLTP nalezen. Jsme měření propustnosti dá dosáhnout databáze spuštěné v každé úrovni výkonu.

Z hlediska jsou vyjádřeny prostředků a výkonu každou úroveň služby a výkonu úroveň [jednotky transakcí databáze (Dtu)](sql-database-what-is-a-dtu.md). Počet jednotek Dtu poskytnout způsob, jak popisují relativní kapacitu úrovně výkonu založené na kombinaci měření procesoru, paměti a čtení a zápisu, nabízí sazby jednotlivými úrovněmi výkonu. Zvýší hodnocení DTU databáze rovná zvýší výkon databáze. Testu výkonnosti umožňuje posoudit dopad na výkon databáze většího výkonu nabízí jednotlivými úrovněmi výkonu výkonem skutečné databázové operace, při škálování velikost databáze, počet uživatelů a transakcí míry v poměru k prostředky zadané do databáze.

Vyjádření propustnost vrstvy služby na úrovni Basic pomocí transakce za hodinu, vrstvě služby na úrovni Standard pomocí transakce za minutu a úroveň služeb Premium použití transakcí za sekundu, umožňuje jednodušší a rychle vytvořit relaci výkon potenciální každý vrstvy služby pro požadavky na aplikace.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelace srovnávacího testu výsledky skutečných výkon databáze
Je důležité si uvědomit, že ASDB, podobně jako všechny srovnávacích testů, je reprezentativní a naznačuje výslednou jenom. Sazby transakce dosáhnout s aplikací srovnávacího testu nesmí být stejné jako ty, které může dosáhnout s jinými aplikacemi. Testu výkonnosti se skládá z kolekce jinou transakci, které typy spouštění schéma obsahující rozsah tabulky a datové typy. Během testu výkonnosti využije stejné základní operace, které jsou společné pro všechny úlohy OLTP, nepředstavuje žádné konkrétní třídě databáze nebo aplikace. Cílem srovnávacího testu je zajistit přiměřené Průvodce relativní výkon databáze, která může být očekávána při škálování nahoru nebo dolů mezi úrovněmi výkonu. Ve skutečnosti databáze jsou různé velikosti a složitost, dojde k jiné mix úloh a bude odpovídat různými způsoby. Například aplikace náročné na vstupně-výstupní operace narazit dříve vstupně-výstupní operace prahové hodnoty nebo narazit aplikace náročná na prostředky procesoru CPU omezení dříve. Není zaručeno, že všechny konkrétní databáze bude škálovat stejným způsobem jako srovnávací test v rámci zvýšení zatížení.

Test výkonnosti a její metody jsou podrobněji popsané v níže.

## <a name="benchmark-summary"></a>Souhrn srovnávacího testu
ASDB měří výkon směs základní databázových operací, které se vyskytují nejčastěji v online transakcí (OLTP) úlohy zpracování. I když srovnávacího testu je navržen s cloud computing v paměti, schématu databáze, pro naplnění dat a transakce byly navrženy široce reprezentativní pro základní prvky nejčastěji používané v OLTP úlohy.

## <a name="schema"></a>Schéma
Schéma je určena pro mít dostatek různých a složitost pro podporu širokou škálu operace. Testu výkonnosti spouští skládá z šesti tabulky databáze. V tabulkách spadají do tří kategorií: pevné velikosti, škálování a rozšiřujících se. Existují dvě tabulky pevné velikosti; tři škálování tabulky; a rostoucí jedna tabulka. Pevné velikosti tabulky obsahovat konstantní počet řádků. Škálování tabulky obsahovat mohutnost, která je úměrná výkon databáze, ale nemění během testu výkonnosti. Rostoucí tabulky je velikost jako škálování na počáteční zatížení, ale pak mohutnost změny v průběhu spuštění testu výkonnosti, jako jsou vloženy a odstranit řádky tabulky.

Schéma obsahuje směs datových typů, včetně typu integer, číselný znak a datum a čas. Schéma obsahuje primární a sekundární klíče, ale nejsou žádné cizí klíče – to znamená, že jsou žádné omezení referenční integrity mezi tabulkami.

Program generování dat generuje data pro počáteční databáze. Celé číslo a číselná data se generují s různými strategie. V některých případech se náhodně distribuují hodnoty v rozsahu. V ostatních případech sadu hodnot je náhodně permutovanou funkci zajistit, že se zachová konkrétní distribuční. Textová pole jsou generovány z vyvážené seznam slov k vytvoření realistické vypadající data.

Databáze je velikost podle "měřítko." Měřítko (zkratka jako SF) určuje mohutnost změny velikosti a rozšiřujících se tabulky. Jak je popsáno níže v části Uživatelé a Pacing, velikost databáze, počet uživatelů a maximální výkon všech škálování v poměru k sobě navzájem.

## <a name="transactions"></a>Transakce
Úlohy se skládá z devíti typy transakcí, jak je znázorněno v následující tabulce. Každou transakci je určena pro zvýraznit konkrétní sadu systému charakteristiky v databázi modul a systému hardwaru, s vysokým kontrastem z dalších transakcí. Tento přístup usnadňuje posoudit dopad na celkový výkon různé součásti. Například "Pro čtení velkou" transakce vytváří velký počet operací čtení z disku.

| Typ transakce | Popis |
| --- | --- |
| Přečtěte si Lite |VYBRAT; v paměti; jen pro čtení |
| Střední pro čtení |VYBRAT; většinou v paměti; jen pro čtení |
| Těžký pro čtení |VYBRAT; většinou není v paměti; jen pro čtení |
| Aktualizace Lite |AKTUALIZACE; v paměti; čtení a zápis |
| Update Heavy |AKTUALIZACE; většinou není v paměti; čtení a zápis |
| Vložení Lite |VLOŽIT; v paměti; čtení a zápis |
| Vložit těžký |VLOŽIT; většinou není v paměti; čtení a zápis |
| Odstranění |ODSTRANIT; směs v paměti a není v paměti; čtení a zápis |
| Těžký procesoru |VYBRAT; v paměti; relativně velké zatížení procesoru; jen pro čtení |

## <a name="workload-mix"></a>Kombinace úloh
Transakce jsou náhodně vybírány ze vyvážené distribuce s následující celkové kombinaci. Celkové kombinace má pro čtení a zápis poměr přibližně 2:1.

| Typ transakce | % Kombinaci |
| --- | --- |
| Přečtěte si Lite |35 |
| Střední pro čtení |20 |
| Těžký pro čtení |5 |
| Aktualizace Lite |20 |
| Update Heavy |3 |
| Vložení Lite |3 |
| Vložit těžký |2 |
| Odstranění |2 |
| Těžký procesoru |10 |

## <a name="users-and-pacing"></a>Uživatelé a interval
Zatížení srovnávacího testu vycházejí z nástroj, který odešle transakce mezi sadu připojení k simulaci chování počet souběžných uživatelů. I když všechna připojení a transakce jsou počítače, které jsou generovány, pro jednoduchost označujeme tato připojení jako "uživatelé." I když každý uživatel pracuje nezávisle na jiných uživatelů, všichni uživatelé provádět stejné cyklus následující kroky:

1. Navázání připojení k databázi.
2. Opakujte, dokud signál, chcete-li ukončit:
   * Vyberte transakce (z náhodně vyvážené distribuce).
   * Provedení vybrané transakce a měření doby odezvy.
   * Počkejte intervalu zpoždění.
3. Zavřete připojení k databázi.
4. Ukončení.

Je náhodně vybrané intervalu zpoždění (v kroku 2c), ale s distribučním, který má v průměru 1.0 sekundu. Každý uživatel může, proto v průměru generovat maximálně jednu transakci za sekundu.

## <a name="scaling-rules"></a>Škálování pravidla
Počet uživatelů, je dáno velikost databáze (v jednotkách měřítko). Je jeden uživatel pro každých pět jednotky měřítko. Z důvodu intervalu zpoždění může jeden uživatel generovat maximálně jednu transakci za sekundu v průměru.

Například-měřítko 500 (SF = 500) databáze bude mít 100 uživatelů a můžete dosáhnout maximálně 100 TPS. K řízení vyšší TPS míra vyžaduje více uživatelů a větší databázi.

Následující tabulka zobrazuje počet uživatelů ve skutečnosti trvalejší pro každou úroveň a výkonu služby.

| Úroveň služby (úroveň výkonu) | Uživatelé | Velikost databáze |
| --- | --- | --- |
| Basic |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2.1 GB |
| Standard (S2) |50 |7.1 GB |
| Premium (P1) |100 |14 GB |
| Premium (P2) |200 |28 GB |
| Premium (P6) |800 |114 GB |

## <a name="measurement-duration"></a>Doba trvání měření
Platný spuštění testu výkonnosti vyžaduje stabilního stavu měření doby trvání alespoň jednu hodinu.

## <a name="metrics"></a>Metriky
Klíčové metriky v testu výkonnosti jsou propustnost a dobu odezvy.

* Propustnost je míra nezbytné výkonu v průběhu testu. Propustnost je uvedená v transakce za jednotku předčasné, počítání všechny typy transakcí.
* Doba odezvy se rozumí míra výkonu předvídatelnost. Omezení času odezvy se liší podle třídy služeb s vyšší třídy služeb s přísnější požadavky na dobu odezvy, jak je uvedeno níže.

| Třída služby | Propustnost měr | Požadavky na dobu odezvy |
| --- | --- | --- |
| Premium |Transakce za sekundu |95. percentil v 0,5 sekund |
| Standard |Transakce za minutu |90. percentil v sekundách 1.0 |
| Basic |Transakce za hodinu |80. percentil v sekundách 2.0 |

## <a name="conclusion"></a>Závěr
Srovnávací test Azure SQL Database měří relativní výkon spuštění pro řadu úrovně dostupných služeb a úrovně výkonu databáze SQL Azure. Testu výkonnosti vykonává směs základní databázových operací, které se vyskytují nejčastěji v online transakcí (OLTP) úlohy zpracování. Podle měření skutečným výkonem, poskytuje testu výkonnosti smysluplnější posoudit dopad na propustnost Změna úrovně výkonu, než je možné pomocí právě výpis prostředky poskytované jednotlivé úrovně, jako je například rychlosti procesoru, velikosti paměti a IOPS. V budoucnu budeme nadále momentální srovnávacího testu rozbalte dostupných dat a rozšíří jeho oboru.

## <a name="resources"></a>Zdroje a prostředky
[Úvod do databáze SQL](sql-database-technical-overview.md)

[Úrovně služeb a úrovně výkonu](sql-database-service-tiers.md)

[Pokyny výkonu pro izolované databáze](sql-database-performance-guidance.md)
