---
title: Tahák pro službu Azure SQL Data Warehouse | Microsoft Docs
description: Tady najdete odkazy a osvědčené postupy, které vám pomůžou s rychlým vytvářením řešení Azure SQL Data Warehouse.
services: sql-data-warehouse
author: acomet
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.component: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: 4ef64b9d4e4e5c7f5a628359a8512dcb61b9c941
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43245889"
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Tahák pro službu Azure SQL Data Warehouse
Tento tahák obsahuje užitečné tipy a osvědčené postupy pro vytváření řešení Azure SQL Data Warehouse. Než začnete, přečtěte si článek [Vzory a antivzory úloh Azure SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns), který podrobně popisuje jednotlivé kroky a vysvětluje, co je služba SQL Data Warehouse, a co není.

Následující obrázek znázorňuje proces návrhu datového skladu:

![Nákres]

## <a name="queries-and-operations-across-tables"></a>Dotazy a operace napříč tabulkami

Pokud předem znáte hlavní operace a dotazy, které se ve vašem datovém skladu budou spouštět, můžete nastavit priority architektury datového skladu pro tyto operace. Mezi tyto dotazy a operace můžou patřit:
* Propojení jedné nebo dvou tabulek faktů s tabulkami dimenzí, filtrování zkombinované tabulky a následné připojení výsledků do datového tržiště.
* Provádění velkých nebo malých aktualizací tabulek faktů.
* Připojení pouze dat k tabulkám.

Znalost typů operací předem vám pomůže optimalizovat návrh tabulek.

## <a name="data-migration"></a>Migrace dat

Nejprve svá data načtěte do služby [Azure Data Lake Store](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store) nebo úložiště objektů blob v Azure. Pak pomocí PolyBase načtěte svá data do pracovní tabulky ve službě SQL Data Warehouse. Použijte následující konfiguraci:

| Návrh | Doporučení |
|:--- |:--- |
| Distribuce | Kruhové dotazování. |
| Indexování | Halda |
| Dělení | Žádný |
| Třída prostředku | largerc nebo xlargerc |

Další informace o [migraci dat], [načítání dat] a [procesu extrakce, načítání a transformace (ELT)](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Distribuované nebo replikované tabulky

Použijte následující strategie v závislosti na vlastnostech tabulek:

| Typ | Skvěle se hodí pro...| Na co si dát pozor|
|:--- |:--- |:--- |
| Replikované | • Malé tabulky dimenzí v hvězdicovém schématu s úložištěm menším než 2 GB po kompresi (přibližně 5násobná komprese) |• V tabulce se provádí velké množství transakcí zápisu (například vložení, operace upsert, odstranění, aktualizace).<br></br>• Často měníte zřizování jednotek datového skladu (DWU).<br></br>• Vaše tabulka obsahuje mnoho sloupců, ale používáte pouze 2 až 3 sloupce.<br></br>• Indexujete replikovanou tabulku. |
| Kruhové dotazování (výchozí) | • Dočasná nebo pracovní tabulka<br></br> • Žádný zřejmý připojovací klíč ani vhodný sloupec |• Nízký výkon kvůli přesunům dat |
| Hodnota hash | • Tabulky faktů<br></br>• Velké tabulky dimenzí |• Distribuční klíč není možné aktualizovat. |

**Tipy:**
* Začněte strategií kruhového dotazování, ale usilujte o strategii distribuce hodnot hash, abyste mohli využít výkonnou paralelní architekturu.
* Ujistěte se, že společné klíče hash mají stejný formát dat.
* Neprovádějte distribuci podle formátu varchar.
* U tabulek dimenzí se společným klíčem hash jako tabulka faktů s častými operacemi spojení je možné provádět distribuci hodnot hash.
* Pomocí *[sys.dm_pdw_nodes_db_partition_stats]* můžete analyzovat případné zkreslení dat.
* Pomocí *[sys.dm_pdw_request_steps]* můžete analyzovat přesuny dat vyvolané dotazy, monitorovat všesměrové vysílání času a prohazování prováděné operacemi. To je užitečné při kontrole distribuční strategie.

Další informace o [replikovaných tabulkách] a [distribuovaných tabulkách].

## <a name="index-your-table"></a>Indexování tabulky

Indexování je užitečné pro rychlé čtení tabulek. Existuje jedinečná sada technologií, které můžete použít podle svých potřeb:

| Typ | Skvěle se hodí pro... | Na co si dát pozor|
|:--- |:--- |:--- |
| Halda | • Pracovní nebo dočasná tabulka<br></br>• Malé tabulky s malým počtem hledání |• Každé hledání prochází celou tabulku. |
| Clusterovaný index | • Tabulky obsahující až 100 milionů řádků<br></br>• Velké tabulky (více než 100 milionů řádků) obsahující pouze 1 až 2 často používané sloupce |• Používá se u replikované tabulky.<br></br>• Máte složité dotazy zahrnující několik operací spojení a seskupení.<br></br>• Provádíte aktualizace indexovaných sloupců, což zabírá paměť. |
| Clusterovaný index columnstore (CCI) (výchozí) | • Velké tabulky (více než 100 milionů řádků) | • Používá se u replikované tabulky.<br></br>• Provádíte rozsáhlé operace aktualizace tabulky.<br></br>• Vytvoříte pro tabulku příliš mnoho oddílů a skupiny řádků nebudou pokrývat různé distribuční uzly a oddíly. |

**Tipy:**
* Kromě clusterovaného indexu můžete chtít přidat neclusterovaný index pro sloupec, který se často používá k filtrování. 
* Při správě paměti v tabulce s CCI buďte opatrní. Při načítání dat chcete, aby uživatel (nebo dotaz) využíval výhod velké třídy prostředků. Nezapomeňte se vyhnout ořezávání a vytváření mnoha malých komprimovaných skupin řádků.
* V Gen2 se tabulky CCI ukládají do mezipaměti místně do výpočetních uzlů za účelem maximalizace výkonu.
* U CCI může docházet ke snížení výkonu kvůli špatné kompresi skupin řádků. Pokud k tomu dojde, znovu svůj CCI sestavte nebo ho reorganizujte. Každá komprimovaná skupina řádků by měla obsahovat alespoň 100 000 řádků. Ideálně by skupina řádků měla obsahovat 1 milion řádků.
* Reorganizaci nebo opětovné sestavení indexů byste měli automatizovat na základě frekvence a velikosti přírůstkového načítání. Jarní úklid je vždy užitečný.
* Pokud chcete oříznout skupinu řádků, myslete strategicky. Jak velké jsou otevřené skupiny řádků? Kolik dat očekáváte, že se bude v nadcházejících dnech načítat?

Další informace o [indexech].

## <a name="partitioning"></a>Dělení
Pokud máte velkou tabulku faktů (více než 1 miliarda řádků), můžete ji rozdělit do oddílů. V 99 % případů by klíč oddílu měl být založený na datu. Buďte opatrní, abyste nevytvořili příliš mnoho oddílů, zejména pokud máte clusterovaný index columnstore.

Výhody dělení můžete využívat u pracovních tabulek, které vyžadují ELT. Usnadňuje to správu životního cyklu dat.
Buďte opatrní, abyste nevytvořili příliš mnoho oddílů dat, zejména u clusterovaného indexu columnstore.

Přečtěte si další informace o [oddílech].

## <a name="incremental-load"></a>Přírůstkové načítání

Pokud se chystáte přírůstkově načítat data, nejprve se ujistěte, že pro načítání dat přidělujete větší třídy prostředků. K automatizaci kanálů ELT do služby SQL Data Warehouse doporučujeme použít PolyBase nebo ADF V2.

V případě velkých dávek aktualizací historických dat nejprve příslušná data odstraňte. Pak proveďte hromadné vložení nových dat. Tento dvoukrokový přístup je efektivnější.

## <a name="maintain-statistics"></a>Udržujte statistiky
 Dokud nebudou obecně dostupné automatické statistky, vyžaduje SQL Data Warehouse ruční údržbu statistik. Statistiky je důležité aktualizovat, když dojde k *významným* změnám vašich dat. Pomáhá to optimalizovat plány dotazů. Pokud zjistíte, že údržba vašich statistik trvá příliš dlouho, pečlivěji zvažte, které sloupce mají statistiku mít. 

Můžete také definovat frekvenci aktualizací. Například můžete chtít každý den aktualizovat sloupce s datem, do kterých se můžou přidávat nové hodnoty. Nejvíce výhod získáte tak, že budete mít statistiky pro sloupce používané ve spojeních, sloupce používané v klauzuli WHERE a sloupce používané v příkazu GROUP BY.

Další informace o [statistikách].

## <a name="resource-class"></a>Třída prostředků
SQL Data Warehouse používá skupiny prostředků jako způsob přidělení paměti pro dotazy. Pokud ke zrychlení dotazů nebo načítání potřebujete více paměti, měli byste přidělit vyšší třídy prostředků. Na druhou stranu, použití větších tříd prostředků má vliv na souběžnost. Na to byste měli brát ohled před přesunem všech vašich uživatelů do větší třídy prostředků.

Pokud si všimnete, že dotazy trvají příliš dlouho, zkontrolujte, jestli vaši uživatelé nepoužívají velké třídy prostředků. Velké třídy prostředků využívají velké množství slotů souběžnosti. Můžou způsobit hromadění dalších dotazů ve frontě.

Z využitím SQL Data Warehouse Gen2 dostává každá třída prostředků 2,5krát víc paměti než Gen1.

Další informace o práci s [třídami prostředků a souběžností].

## <a name="lower-your-cost"></a>Snížení nákladů
Klíčovou funkcí služby SQL Data Warehouse je schopnost [spravovat výpočetní prostředky](sql-data-warehouse-manage-compute-overview.md). Datový sklad můžete pozastavit, když ho zrovna nepoužíváte, a zastavit tak účtování výpočetních prostředků. Prostředky můžete škálovat s ohledem na své požadavky na výkon. K pozastavení můžete použít [Azure Portal](pause-and-resume-compute-portal.md) nebo [PowerShell](pause-and-resume-compute-powershell.md). Ke škálování můžete použít [Azure Portal](quickstart-scale-compute-portal.md), [Powershell](quickstart-scale-compute-powershell.md), jazyk [T-SQL](quickstart-scale-compute-tsql.md) nebo rozhraní [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Automatické škálování teď můžete provádět kdykoli díky Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Optimalizace výkonu architektury

Služby SQL Database a Azure Analysis Services doporučujeme zvážit v hvězdicovité architektuře. Toto řešení může zajistit izolaci úloh mezi různými skupinami uživatelů a zároveň využívat pokročilé funkce zabezpečení ve službách SQL Database a Azure Analysis Services. Tímto způsobem můžete uživatelům poskytnout neomezenou souběžnost.

Další informace o [typických architekturách, které využívají službu SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/).

Nasazujte své paprsky do databází SQL ze služby SQL Data Warehouse jedním kliknutím:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Nákres]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[načítání dat]:design-elt-data-loading.md
[deeper guidance]:guidance-for-loading-data.md
[indexech]:sql-data-warehouse-tables-index.md
[oddílech]:sql-data-warehouse-tables-partition.md
[statistikách]:sql-data-warehouse-tables-statistics.md
[třídami prostředků a souběžností]:resource-classes-for-workload-management.md
[replikovaných tabulkách]:design-guidance-for-replicated-tables.md
[distribuovaných tabulkách]:sql-data-warehouse-tables-distribute.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[migraci dat]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

[Azure Data Lake Store]: ../data-factory/connector-azure-data-lake-store.md
[sys.dm_pdw_nodes_db_partition_stats]: /sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
