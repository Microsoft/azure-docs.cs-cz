---
title: "Tahák pro Azure SQL Data Warehouse | Microsoft Docs"
description: "Najděte odkazy a osvědčené postupy pro rychle vytvářet řešení Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/20/2018
ms.author: acomet
ms.openlocfilehash: c67d56ff63f70baa052be17c119d943c558d398f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Tahák pro Azure SQL Data Warehouse
Chcete-li list podvést poskytuje užitečné tipy a osvědčené postupy pro vytváření řešení Azure SQL Data Warehouse. Před zahájením práce, další informace o jednotlivých kroků podrobně načtením [vzory úlohy Azure SQL Data Warehouse a proti vzory](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns), která vysvětluje, co je SQL Data Warehouse a co není.

Následující obrázek znázorňuje proces návrhu datového skladu:

![Nákresu]

## <a name="queries-and-operations-across-tables"></a>Dotazy a operace mezi tabulkami

Pokud znáte předem primární operace a dotazy ke spuštění v datovém skladu, můžete změnit prioritu vaší Architektura datového skladu pro tyto operace. Tyto dotazy a operace mohou zahrnovat:
* Propojení s tabulek dimenzí, filtrování kombinované tabulky a pak výsledky připojit do datového tržiště jedno nebo dvě tabulky faktů.
* Provádění aktualizací do prodeje fakt velký či malý.
* Připojení k vaší tabulky jenom data.

Znalost typy operací v předstihu vám pomůže optimalizovat návrh vaší tabulek.

## <a name="data-migration"></a>Migrace dat

Nejdřív načíst data do [Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store) nebo úložiště objektů Blob v Azure. Potom pomocí funkce PolyBase načteme data do SQL Data Warehouse v pracovní tabulce. Použijte následující konfiguraci:

| Návrh | Doporučení |
|:--- |:--- |
| Distribuce | Kruhové dotazování. |
| Indexování | Haldy |
| Dělení | Žádné |
| Třída prostředku | largerc nebo xlargerc |

Další informace o [migrace dat], [načítání dat]a [proces extrakce, načítání a transformace ELT ()](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Distribuované nebo replikované tabulky

Pomocí následujících strategií, v závislosti na její vlastnosti:

| Typ | Skvělé vyhovovat...| Sledujte if...|
|:--- |:--- |:--- |
| Replikovat | • Tabulky malé dimenzí v hvězdicového schématu s menší než 2 GB úložiště po kompresi (komprese ~ 5 x) |• Mnoho zapisují transakce jsou v tabulce (například vložení, upsert, odstranění, aktualizace)<br></br>• Změňte zřizování často datového skladu jednotky (DWU)<br></br>• Používáte pouze sloupce 2 – 3 ale vaše tabulka má mnoho sloupců<br></br>• Indexu replikované tabulky |
| Kruhové dotazování (výchozí) | • Dočasných nebo pracovní tabulka<br></br> • Žádné zřejmé připojení sloupec klíče nebo dobrý candidate |• Výkon je pomalý z důvodu přesunu dat. |
| Hash | Tabulky faktů •<br></br>• Velké dimenze tabulky |• Distribučního klíče nelze aktualizovat. |

**Tipy pro:**
* Spustit s kruhové dotazování, ale aspire na strategii hash distribuce využívat výhod masivně paralelní architektura.
* Ujistěte se, že společné klíče hash mají stejný formát data.
* Nemáte distribuovat na varchar formátu.
* Tabulky dimenzí pomocí běžných hash klíče do tabulky faktů s operací spojování časté lze distribuovat algoritmu hash.
* Použití  *[sys.dm_pdw_nodes_db_partition_stats]*  k analýze všechny hodnoty v datech.
* Použití  *[sys.dm_pdw_request_steps]*  k analýze dat pohybů za dotazy, monitorovat čas vysílání a náhodně operací trvat. To je užitečné zkontrolovat strategie distribuce.

Další informace o [replikovaných tabulek] a [distribuované tabulky].

## <a name="index-your-table"></a>Index tabulky

Indexování je užitečné pro rychlé čtení tabulky. Je jedinečnou sadu technologií, které můžete použít na základě potřeb:

| Typ | Skvělé vyhovovat... | Sledujte if...|
|:--- |:--- |:--- |
| Haldy | • Pracovní nebo dočasné tabulky<br></br>• Malé tabulky s malé hledání |• Žádné vyhledávání kontrol plném tabulka |
| Clusterovaný index | • Tabulky s až 100 milionu řádků<br></br>• Velké tabulky (více než 100 miliónů řádky) s výraznou použít jenom na 1 – 2 sloupce |• Použít na replikované tabulky<br></br>• Máte komplexní dotazy zahrnující více připojení a Group By operace<br></br>• Proveďte aktualizace na indexovaného sloupce: trvá paměti |
| Clusterovaný index columnstore (KÚS) (výchozí) | • Velké tabulky (více než 100 miliónů řádky) | • Použít na replikované tabulky<br></br>•, Které provedete masivní aktualizovat operací na tabulku<br></br>• Overpartition tabulku: skupiny řádků napříč uzly jiný distribuční a oddíly, které nezahrnují. |

**Tipy pro:**
* Nad clusterovaný index můžete přidat do sloupce výraznou použitých pro filtrování neclusterovaný index. 
* Dávejte pozor, jak spravovat paměti v tabulce s KÚS. Při načítání dat, budete chtít uživatele (nebo dotaz) těžit z třídy velké prostředku. Ujistěte se, že vyhnout ořezávání a vytváření skupin mnoho malých komprimované řádků.
* Optimalizovaná pro výpočetní vrstvě skály s KÚS.
* Pro KÚS může dojít, pomalý výkon z důvodu nízký komprese vaší skupiny řádků. Pokud k tomu dojde, znovu sestavit nebo reorganizovat vaší KÚS. Chcete nejméně 100 000 řádků na jeden skupiny komprimované řádků. Ideální je 1 milionu řádků v skupiny řádků.
* Na základě frekvence přírůstkových zatížení a velikosti, chcete automatizovat při reorganizovat nebo opětovné sestavení indexů. Čištění pružiny je vždy užitečné.
* Být strategické Pokud chcete trim skupiny řádků. Jak velká se o skupiny, otevřete řádek? Množství dat, které očekáváte načíst v následujících dnech?

Další informace o [indexy].

## <a name="partitioning"></a>Dělení
Vaše tabulka může oddílu, když máte velké fakt tabulku (větší než 1 miliardy řádků). V 99 procent případů klíč oddílu by měla být založena na datum. Pečlivě není overpartition, zejména pokud máte clusterovaný index columnstore.

S pracovních tabulek, které vyžadují ELT, můžete využívat výhod rozdělení do oddílů. Zařídí dat – Správa životního cyklu.
Dejte pozor, abyste overpartition vaše data, zejména u clusterovaný index columnstore.

Další informace o [oddíly].

## <a name="incremental-load"></a>Přírůstkové zatížení

Pokud se chystáte přírůstkově načíst vaše data, nejprve ujistěte se, že přidělíte větší třídy prostředků k načtení dat. Doporučujeme používat pro automatizaci kanály ELT do SQL Data Warehouse PolyBase a ADF V2.

Pro velké hromadné aktualizace ve vašich historických dat nejprve odstraňte obavy data. Zkontrolujte příkaz bulk insert nových dat, potom. Tento přístup dvoustupňové je efektivnější.

## <a name="maintain-statistics"></a>Udržujte statistiky
 Dokud automaticky statistiky jsou obecně k dispozici, SQL Data Warehouse vyžaduje ruční údržbu statistiky. Je potřeba aktualizovat statistiku jako *významné* změny dojít k datům. To pomáhá Optimalizace plánu dotazu. Pokud zjistíte, že trvá příliš dlouho udržovat všechny vaše statistiky, pečlivě více sloupců, které mají statistiky. 

Můžete také definovat frekvence aktualizace. Například můžete chtít aktualizovat sloupců s kalendářními daty, kde může přidat nové hodnoty, na každý den. Získáte tak, že statistiky na sloupce použité ve spojení, sloupce použité v klauzuli WHERE a sloupců v GROUP BY nalezen využívat výhod.

Další informace o [statistiky].

## <a name="resource-class"></a>Třída prostředků
SQL Data Warehouse používá skupiny prostředků jako způsob přidělení paměti pro dotazy. Pokud potřebujete více paměti pro zlepšení dotazu nebo načtení rychlost, měli byste přidělit vyšší třídy prostředků. Na straně překlopit pomocí větší třídy prostředků ovlivňuje souběžnosti. Chcete, vzít v úvahu před přesunutím všichni uživatelé na třídu velké prostředků.

Pokud si všimnete, že dotazy trvá příliš dlouho, zkontrolujte, že uživatelé se nespustí v třídách velké prostředků. Třídy velké prostředků využívat mnoho sloty souběžnosti. Může dojít k jiné dotazy do fronty.

Nakonec pomocí vrstvě výpočetní optimalizované dostane každá třída prostředků 2, 5krát větší spotřebu paměti než na vrstvě elastické optimalizované.

Další informace o tom, jak pracovat s [třídy prostředků a souběžnost].

## <a name="lower-your-cost"></a>Snížení nákladů na vaše
Klíčovou funkcí SQL Data Warehouse je schopnost [spravovat výpočetní prostředky](sql-data-warehouse-manage-compute-overview.md). Je možné pozastavit datový sklad když nepoužíváte, která zastaví fakturaci výpočetní prostředky. Je možné škálovat materiály, které splňují vaše požadavky na výkon. Chcete-li pozastavit, použijte [portál Azure](pause-and-resume-compute-portal.md) nebo [prostředí PowerShell](pause-and-resume-compute-powershell.md). Pokud chcete použít škálování, použijte [portál Azure](quickstart-scale-compute-portal.md), [prostředí Powershell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md), nebo [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Škálování teď v době, je vhodné s Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Optimalizace výkonu vaší architektury

Doporučujeme, abyste vzhledem k tomu SQL Database a Azure Analysis Services v architektuře střed a paprsek. Toto řešení může poskytovat zatížení izolaci mezi skupinami jiného uživatele při také pomocí funkce Rozšířené zabezpečení z SQL Database a Azure Analysis Services. Toto je způsob, jak uživatelům poskytnout neomezený souběžnosti.

Další informace o [typické architektury, které využívají služby SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/).

Nasaďte jedním kliknutím vaší koncových v databázích SQL z SQL Data Warehouse:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Nákresu]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[načítání dat]:design-elt-data-loading.md
[deeper guidance]:guidance-for-loading-data.md
[indexy]:sql-data-warehouse-tables-index.md
[oddíly]:sql-data-warehouse-tables-partition.md
[statistiky]:sql-data-warehouse-tables-statistics.md
[třídy prostředků a souběžnost]:resource-classes-for-workload-management.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[migrace dat]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[replikovaných tabulek]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[distribuované tabulky]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[Azure Data Lake Store]: https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
