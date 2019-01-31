---
title: Azure SQL Data Warehouse zpráva k vydání verze. října 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 81096eeb1edcd6681bdc887d267ee477d78a8578
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472078"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Říjen 2018
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny v října 2018.

## <a name="devops-for-data-warehousing"></a>DevOps pro datové sklady
Vysoce požadované funkce pro SQL Data Warehouse (datový Sklad SQL) je teď ve verzi preview s podporou pro SQL Server Data nástroje (SSDT) v sadě Visual Studio! Týmy vývojářů můžou teď spolupracovat přes codebase jeden, správou verzí a rychle nasadit změny do jakékoli instance na světě. Uvažujete o připojení? Tato funkce je dostupná ve verzi preview ještě dnes! Můžete si zaregistrovat [SQL Data Warehouse Visual Studio SQL Server Data Tools (SSDT) – formulář pro registraci ve verzi Preview](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u). Zadaný vysokému zatížení, jsme spravujete přijetí do preview zajišťují optimální prostředí pro naše zákazníky. Jakmile se zaregistrujete, Naším cílem je ověřte svůj status do 7 pracovních dnů.

## <a name="row-level-security-generally-available"></a>Zabezpečení na úrovni řádků obecně dostupná
Azure SQL Data Warehouse (SQL data Warehouse) teď podporuje zabezpečení na úrovni řádků (RLS) přidává výkonné funkce pro zabezpečení citlivých dat. Zavedení projektového systému zabezpečení na úrovni řádků můžete implementovat zásady zabezpečení pro řízení přístupu k řádkům v tabulkách, stejně jako v přístupem jaké řádků. Zabezpečení na úrovni řádků umožňuje tento ovládací prvek velice přesně kontrolovat přístup bez nutnosti přepracovat váš datový sklad. Zabezpečení na úrovni řádků zjednodušuje celkový model zabezpečení logiky omezení přístupu se nachází v databázové vrstvě samotné, nikoli od data v jiné aplikaci. Zabezpečení na úrovni řádků také eliminuje nutnost zavedení zobrazení k vyfiltrování řádků pro správu řízení přístupu. Se neúčtují žádné další poplatky pro tuto funkci zabezpečení na podnikové úrovni pro všechny naše zákazníky.

## <a name="advanced-advisors"></a>Pokročilé poradci
Pokročilé ladění pro Azure SQL Data Warehouse (SQL data Warehouse), stačí informací snazší se další data warehouse doporučení a metriky. Existují další pokročilé doporučení ohledně výkonu pro prostřednictvím Azure Advisoru vašim službám, včetně:
1.  Adaptivní mezipaměti – informováni, pokud škálování optimalizovat využití mezipaměti.
2.  Distribuce tabulky – určuje, kdy se k replikaci tabulek pro omezení přesunu dat a zvýšení výkonu úloh. 
3.  Databáze tempdb – Principy při škálování a nakonfigurovat prostředek třídy za účelem omezení kolizí databázi tempdb.

Je hlubší integraci metriky datového skladu s [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) včetně rozšířené přizpůsobitelný graf sledování pro téměř v reálném čase metrik v okně Přehled. Když monitorujete využití nebo ověřujete a využíváte doporučení pro datový sklad, pro přístup k metrikám Azure Monitoru už nemusíte opouštět okno s přehledem datového skladu. Navíc k dispozici nové metriky, jako jsou databáze tempdb a adaptivní mezipaměti využití doplnit vaše doporučení k výkonu.

## <a name="advanced-tuning-with-integrated-advisors"></a>Pokročilé ladění s integrované poradci
Pokročilé ladění pro Azure SQL Data Warehouse (SQL data Warehouse), stačí je teď jednodušší s další data warehouse doporučení a metriky a verzí portálu přehled okna, která poskytuje integrované prostředí pomocí Azure Advisoru a Azure Monitor.

## <a name="accelerated-database-recovery-adr"></a>Obnovení databáze akcelerace (ADR)
Azure SQL Data Warehouse Accelerated databáze obnovení (ADR) je teď ve verzi Public Preview. Pravidla automatického nasazení je nový modul serveru SQL, která výrazně zlepšuje dostupnost databáze, zejména v přítomnost dlouhotrvající transakce, tak zcela realizace aktuální proces obnovení zdola nahoru. Hlavní výhody pravidla automatického nasazení se obnovení databáze rychlé a konzistentní vzhledem k aplikacím a odvolání okamžité transakce.

## <a name="azure-monitor-diagnostics-logs"></a>Protokoly diagnostiky Azure Monitor
SQL Data Warehouse (SQL data Warehouse) teď umožňuje lepší přehled o analytické úlohy díky integraci přímo s diagnostické protokoly Azure monitoru. Tato nová funkce vývojářům umožňuje analyzovat chování úloh s za delší časové období a provádět informovaná rozhodnutí týkající se správy optimalizace nebo kapacity dotazu. Zavedli jsme teď na externí protokolování procesu prostřednictvím [diagnostické protokoly Azure monitoru](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs) , poskytování dalších přehledů o vašich úloh datového skladu. Jediným kliknutím na tlačítko, je nyní možné konfigurace diagnostických protokolů pro řešení potíží s použitím možnosti výkonu dotazů na historii [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Diagnostické protokoly Azure monitoru podporu přizpůsobitelné období uložením protokoly do účtu úložiště pro účely auditování možnost streamování protokolů do služby event hubs téměř přehledy v reálném čase telemetrii a možnost Analýza protokolů pomocí služby Log Analytics pomocí dotazů na protokoly. Diagnostické protokoly se skládají z telemetrických zobrazení vašeho datového skladu, která jsou ekvivalentní nejčastěji využívaným zobrazením dynamické správy pro řešení potíží s výkonem pro SQL Data Warehouse. Pro tato počáteční verze jsme povolili zobrazení pro následující systémová zobrazení dynamické správy:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Správa paměti Columnstore
Jak se zvyšuje počet skupin řádků komprimované sloupec úložiště, paměť požadovanou ke správě metadat segmentu interní sloupec pro tyto rowgroups zvyšuje.  V důsledku toho může snížit výkon dotazů a dotazy pro některé Columnstore dynamických zobrazení správy (DMV).  Vylepšení provedli v této verzi k optimalizaci velikosti interní metadata pro tyto případy, což vede k lepší prostředí a výkon těchto dotazů. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Integrace se službou Azure Data Lake Storage Gen2 (GA)
Azure SQL Data Warehouse (SQL data Warehouse) teď má nativní integraci s Azure Data Lake Storage Gen2. Zákazníci mohou nyní načíst data pomocí externích tabulek z ABFS do SQL data Warehouse. Tato funkce umožňuje zákazníkům integrovat svoje datová jezera v Data Lake Storage Gen2. 

## <a name="bug-fixes"></a>Opravy chyb

| Název | Popis |
|:---|:---|
| **CETAS Parquet chybám malých prostředků třídy datových skladů DW2000 a provádění dalších akcí** | Tato oprava správně identifikuje nulový odkaz na vytvoření externí tabulky lze případně na cestu kódu Parquet. |
|**Hodnota sloupce identity můžou přijít o některé operace CTAS** | Hodnota sloupce identifikovat nemusí být zachována při CTASed do jiné tabulky. V blogu: [ https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/ ](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/). |
| **V některých případech, kdy relace se ukončí při běhu dotazu došlo k vnitřní chybě.** | Tato oprava aktivuje InvalidOperationException, pokud relace se ukončí, když dotaz stále běží. |
| **(Nasazené v listopadu 2018) Zákazníci zaznamenávali neoptimální výkon při pokusu o načtení více malých souborů ze služby ADLS (Gen1) při použití technologie Polybase.** | Výkon systému byla bottlenecked během ověřování tokenů zabezpečení AAD. Problémy s výkonem byly zmírnit tím, že umožňuje ukládání do mezipaměti tokenů zabezpečení. |


## <a name="next-steps"></a>Další postup
Teď, když už víte o něco o SQL Data Warehouse, zjistěte, jak rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]. Pokud s Azure začínáte, může vám být užitečný [Glosář Azure][Azure glossary], kde najdete potřebnou terminologii. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

* [Úspěšné zákaznické implementace]
* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy zákaznického poradního týmu]
* [Fórum Stack Overflow]
* [Twitter]


[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Úspěšné zákaznické implementace]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
