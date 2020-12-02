---
title: Tahák list pro vyhrazený fond SQL (dříve SQL DW)
description: Najděte odkazy a osvědčené postupy pro rychlé vytvoření vyhrazeného fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a236cf99d3131e83619cfab06e8ec028938a87ba
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454624"
---
# <a name="cheat-sheet-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytic"></a>Tahák list pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics

Tento tahák list poskytuje užitečné tipy a osvědčené postupy pro vytváření vyhrazených řešení fondu SQL (dřív SQL DW).

Následující obrázek znázorňuje proces návrhu datového skladu s vyhrazeným fondem SQL (dříve SQL DW):

![Nákres](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Dotazy a operace napříč tabulkami

Pokud předem znáte hlavní operace a dotazy, které se ve vašem datovém skladu budou spouštět, můžete nastavit priority architektury datového skladu pro tyto operace. Mezi tyto dotazy a operace můžou patřit:

* Propojení jedné nebo dvou tabulek faktů s tabulkami dimenzí, filtrování zkombinované tabulky a následné připojení výsledků do datového tržiště.
* Provádění velkých nebo malých aktualizací tabulek faktů.
* Připojení pouze dat k tabulkám.

Znalost typů operací předem vám pomůže optimalizovat návrh tabulek.

## <a name="data-migration"></a>Migrace dat

Nejdřív načtěte data do [Azure Data Lake Storage](../../data-factory/connector-azure-data-lake-store.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) nebo Azure Blob Storage. Dále pomocí [příkazu copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (Preview) načtěte data do pracovních tabulek. Použijte následující konfiguraci:

| Návrh | Doporučení |
|:--- |:--- |
| Distribuce | Kruhové dotazování |
| Indexování | Halda |
| Dělení | Žádné |
| Třída prostředku | largerc nebo xlargerc |

Další informace o [migraci dat](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), [načítání dat](design-elt-data-loading.md) a [procesu extrakce, načítání a transformace (ELT)](design-elt-data-loading.md).

## <a name="distributed-or-replicated-tables"></a>Distribuované nebo replikované tabulky

Použijte následující strategie v závislosti na vlastnostech tabulek:

| Typ | Skvěle se hodí pro tyto účely...| Na co si dát pozor|
|:--- |:--- |:--- |
| Replikované | * Malé tabulky dimenzí ve schématu hvězdičky s méně než 2 GB úložiště po kompresi (~ pětinásobné Compression) |* Mnoho transakcí zápisu je v tabulce (například vložení, Upsert, odstranit, aktualizovat).<br></br>* Často se zřizování jednotek datového skladu (DWU) mění.<br></br>* Používáte jenom 2-3 sloupců, ale tabulka má mnoho sloupců.<br></br>* Naindexuje replikovanou tabulku. |
| Kruhové dotazování (výchozí) | * Dočasná/pracovní tabulka<br></br> * Žádný zjevně se nepřipojuje klíč ani sloupec s dobrým kandidátem |* Výkon je pomalý kvůli přesunu dat. |
| Hodnoty hash | * Tabulky faktů<br></br>* Velké tabulky dimenzí |* Distribuční klíč nelze aktualizovat |

**Tip**

* Začněte strategií kruhového dotazování, ale usilujte o strategii distribuce hodnot hash, abyste mohli využít výkonnou paralelní architekturu.
* Ujistěte se, že společné klíče hash mají stejný formát dat.
* Nedistribuovat ve formátu varchar.
* U tabulek dimenzí se společným klíčem hash jako tabulka faktů s častými operacemi spojení je možné provádět distribuci hodnot hash.
* Pomocí *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* můžete analyzovat případné zkreslení dat.
* *[Sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* můžete použít k analýze pohybů dat na dotazech, sledování času a náhodnému provedení operací. To je užitečné při kontrole distribuční strategie.

Další informace o [replikovaných tabulkách](design-guidance-for-replicated-tables.md) a [distribuovaných tabulkách](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Indexování tabulky

Indexování je užitečné pro rychlé čtení tabulek. Existuje jedinečná sada technologií, které můžete použít podle svých potřeb:

| Typ | Skvěle se hodí pro tyto účely... | Na co si dát pozor|
|:--- |:--- |:--- |
| Halda | * Pracovní/dočasná tabulka<br></br>* Malé tabulky s malým vyhledáváním |* Jakékoli vyhledávání prohledává celou tabulku. |
| Clusterovaný index | * Tabulky s až 100 000 000 řádky<br></br>* Velké tabulky (více než 100 000 000 řádků) s velkým využitím pouze 1-2 sloupců |* Používá se v replikované tabulce.<br></br>* Máte složité dotazy zahrnující vícenásobné operace JOIN a Group by<br></br>* Provedete aktualizace indexovaných sloupců: přebírá paměť. |
| Clusterovaný index columnstore (CCI) (výchozí) | * Velké tabulky (více než 100 000 000 řádků) | * Používá se v replikované tabulce.<br></br>* V tabulce provedete rozsáhlé operace aktualizace.<br></br>* Předělíte tabulku: skupiny řádků nejsou v různých distribučních uzlech a oddílech. |

**Tip**

* Kromě clusterovaného indexu můžete chtít přidat neclusterovaný index pro sloupec, který se často používá k filtrování.
* Při správě paměti v tabulce s CCI buďte opatrní. Při načítání dat chcete, aby uživatel (nebo dotaz) využíval výhod velké třídy prostředků. Nezapomeňte se vyhnout ořezávání a vytváření mnoha malých komprimovaných skupin řádků.
* V Gen2 se tabulky CCI ukládají do mezipaměti místně do výpočetních uzlů za účelem maximalizace výkonu.
* U CCI může docházet ke snížení výkonu kvůli špatné kompresi skupin řádků. Pokud k tomu dojde, znovu svůj CCI sestavte nebo ho reorganizujte. Každá komprimovaná skupina řádků by měla obsahovat alespoň 100 000 řádků. Ideálně by skupina řádků měla obsahovat 1 milion řádků.
* Reorganizaci nebo opětovné sestavení indexů byste měli automatizovat na základě frekvence a velikosti přírůstkového načítání. Jarní úklid je vždy užitečný.
* Pokud chcete oříznout skupinu řádků, myslete strategicky. Jak velké jsou otevřené skupiny řádků? Kolik dat očekáváte, že se bude v nadcházejících dnech načítat?

Další informace o [indexech](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Dělení

Pokud máte velkou tabulku faktů (více než 1 miliarda řádků), můžete ji rozdělit do oddílů. V 99 % případů by klíč oddílu měl být založený na datu. Buďte opatrní, abyste nevytvořili příliš mnoho oddílů, zejména pokud máte clusterovaný index columnstore.

Výhody dělení můžete využívat u pracovních tabulek, které vyžadují ELT. Usnadňuje to správu životního cyklu dat.
Buďte opatrní, abyste nevytvořili příliš mnoho oddílů dat, zejména u clusterovaného indexu columnstore.

Přečtěte si další informace o [oddílech](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Přírůstkové načítání

Pokud se chystáte přírůstkově načítat data, nejprve se ujistěte, že pro načítání dat přidělujete větší třídy prostředků.  To je důležité hlavně při načítání do tabulek pomocí clusterovaných indexů columnstore.  Další podrobnosti naleznete v tématu [třídy prostředků](resource-classes-for-workload-management.md) .  

Pro automatizaci vašich ELTch kanálů do datového skladu doporučujeme použít základní a ADF v2.

Pro velkou dávku aktualizací v historických datech zvažte použití [CTAS](sql-data-warehouse-develop-ctas.md) k zapsání dat, která chcete uchovávat v tabulce, a nepoužívejte vložení, aktualizaci a odstranění.

## <a name="maintain-statistics"></a>Udržujte statistiky

Statistiky je důležité aktualizovat, když dojde k *významným* změnám vašich dat. Pokud chcete zjistit, jestli došlo k *významným* změnám, přečtěte si téma [aktualizace](sql-data-warehouse-tables-statistics.md#update-statistics) . Aktualizovaná Statistika optimalizuje vaše plány dotazů. Pokud zjistíte, že údržba vašich statistik trvá příliš dlouho, pečlivěji zvažte, které sloupce mají statistiku mít.

Můžete také definovat frekvenci aktualizací. Například můžete chtít každý den aktualizovat sloupce s datem, do kterých se můžou přidávat nové hodnoty. Nejvíce výhod získáte tak, že budete mít statistiky pro sloupce používané ve spojeních, sloupce používané v klauzuli WHERE a sloupce používané v příkazu GROUP BY.

Další informace o [statistikách](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Třída prostředků

Skupiny prostředků se používají jako způsob přidělování paměti pro dotazy. Pokud ke zrychlení dotazů nebo načítání potřebujete více paměti, měli byste přidělit vyšší třídy prostředků. Na druhou stranu, použití větších tříd prostředků má vliv na souběžnost. Na to byste měli brát ohled před přesunem všech vašich uživatelů do větší třídy prostředků.

Pokud si všimnete, že dotazy trvají příliš dlouho, zkontrolujte, jestli vaši uživatelé nepoužívají velké třídy prostředků. Velké třídy prostředků využívají velké množství slotů souběžnosti. Můžou způsobit hromadění dalších dotazů ve frontě.

Nakonec při použití Gen2 [vyhrazeného fondu SQL (dříve SQL DW)](sql-data-warehouse-overview-what-is.md)každá třída prostředků získá 2,5 krát více paměti než Gen1.

Další informace o práci s [třídami prostředků a souběžností](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Snížení nákladů

Klíčovou funkcí služby Azure synapse je schopnost [Spravovat výpočetní prostředky](sql-data-warehouse-manage-compute-overview.md). Vyhrazený fond SQL (dřív SQL DW) můžete pozastavit, když ho nepoužíváte, což zastaví fakturaci výpočetních prostředků. Prostředky můžete škálovat s ohledem na své požadavky na výkon. K pozastavení můžete použít [Azure Portal](pause-and-resume-compute-portal.md) nebo [PowerShell](pause-and-resume-compute-powershell.md). Ke škálování použijte [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md)nebo [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Automatické škálování teď můžete provádět kdykoli díky Azure Functions:

[![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

## <a name="optimize-your-architecture-for-performance"></a>Optimalizace výkonu architektury

Služby SQL Database a Azure Analysis Services doporučujeme zvážit v hvězdicovité architektuře. Toto řešení může zajistit izolaci úloh mezi různými skupinami uživatelů a zároveň využívat pokročilé funkce zabezpečení ve službách SQL Database a Azure Analysis Services. Tímto způsobem můžete uživatelům poskytnout neomezenou souběžnost.

Přečtěte si víc o [typických architekturách, které využívají vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/).

Nasaďte v jednom kliknutím své paprsky v databázích SQL z vyhrazeného fondu SQL (dřív SQL DW):

[![Obrázek znázorňující tlačítko s názvem "nasadit do Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json)
